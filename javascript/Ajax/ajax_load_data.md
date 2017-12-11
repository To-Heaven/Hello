# 使用Ajax加载数据实现页面渲染

#### Ajax or 模板？
- 一般在web开发中，页面渲染用到最多的就是使用Web框架提供的模板来渲染页面，在渲染好页面之后再将页面放在响应中返回，这会消耗服务器的性能资源，下面介绍一种更为灵活并且扩展性更好的方式来实现页面渲染

#### 使用Ajax
- 在页面加载完之后，我们使用onload时间来触发Ajax请求，像服务端请求数据，服务端只需要返回后端所期望的数据结构（这个数据结构一般得要有利于前端实现），再使用JavaScript来渲染页面，这样设计的话，前后端主要业务逻辑是这样的
	- 前端负责请求数据，并使用JavaScript渲染（包括渲染标签及其属性）
	- 后端负责返回响应的数据结构（存放了标签的属性及数据）

## 举例
#### 这里举例会议室预定项目中对`table`标签的渲染
- 数据结构预览
	- 假设每天由4个时间端用来发开会议室预定，分别是
		- `8：00~10：00`
		- `10：00~12：00`
		- `14：00~16：00`
		- `16：00~18：00`

```
[
	[{"text": "会议室名称1", "attrs": {}}, {"text": "", "attrs": {}},, {"text": "预定人", "attrs": {"class": "active", "period_id": 5, "room_id": 1}}, {"text": "", "attrs": {}}, {"text": ""}],			# 表示在10:00~12:00时间段，预定会议室1，该预定记录在预定表中存放的id为5，对饮更的会议室id为5
	[{"text": "会议室名称2", "attrs": {}}, "text": "预定人", "attrs": {"class": "active", "period_id": 6, "room_id": 2}}, {"text": "", "attrs": {}}, {"text": "", "attrs": {}}, {"text": "", "attrs": {}}],
]
```

###### 后端生成


```
@auth_json
def booking(request):
    """
    获取会议室预定情况以及预定会议室
    :param request: 
    :param date: 
    :return: 
    """
    ret = {'code': 1000, 'msg': None, 'data': None}
    current_date = datetime.datetime.now().date()

    if request.method == "GET":
        try:
            fetch_date = request.GET.get('date')
            fetch_date = datetime.datetime.strptime(fetch_date, '%Y-%m-%d').date()
            if fetch_date < current_date:
                raise Exception('查询时间不能是以前的时间')


            booking_list = models.Booking.objects.filter(booking_date=fetch_date).select_related('user',
                                                                                                 'room').order_by(
                'booking_time')

            booking_dict = {}
            for item in booking_list:
                if item.room_id not in booking_dict:
                    booking_dict[item.room_id] = {item.booking_time: {'name': item.user.name, 'id': item.user.id}}
                else:
                    if item.booking_time not in booking_dict[item.room_id]:
                        booking_dict[item.room_id][item.booking_time] = {'name': item.user.name, 'id': item.user.id}
            """
            {
                room_id:{
                    time_id:{''},
                    time_id:{''},
                    time_id:{''},
                }
            }
            """

            room_list = models.MeetingRoom.objects.all()

            booking_info = []
            for room in room_list:
                temp = [{'text': room.title, 'attrs': {'rid': room.id}, 'chosen': False}]
                for choice in models.Booking.time_choices:
                    v = {'text': '', 'attrs': {'time-id': choice[0], 'room-id': room.id}, 'chosen': False}
                    if room.id in booking_dict and choice[0] in booking_dict[room.id]:
                        v['text'] = booking_dict[room.id][choice[0]]['name']
                        v['chosen'] = True
                        if booking_dict[room.id][choice[0]]['id'] != request.session['user_info']['id']:
                            v['attrs']['disable'] = 'true'
                    temp.append(v)
                booking_info.append(temp)

            ret['data'] = booking_info
        except Exception as e:
            ret['code'] = 1001
            ret['msg'] = str(e)
        return JsonResponse(ret)
    else:
        try:
            booking_date = request.POST.get('date')
            booking_date = datetime.datetime.strptime(booking_date, '%Y-%m-%d').date()
            if booking_date < current_date:
                raise Exception('查询时间不能是以前的时间')


            booking_info = json.loads(request.POST.get('data'))

            for room_id, time_id_list in booking_info['add'].items():
                if room_id not in booking_info['del']:
                    continue
                for time_id in list(time_id_list):
                    if time_id in booking_info['del'][room_id]:
                        booking_info['del'][room_id].remove(time_id)
                        booking_info['add'][room_id].remove(time_id)

            add_booking_list = []
            for room_id, time_id_list in booking_info['add'].items():
                for time_id in time_id_list:
                    obj = models.Booking(
                        user_id=request.session['user_info']['id'],
                        room_id=room_id,
                        booking_time=time_id,
                        booking_date=booking_date
                    )
                    add_booking_list.append(obj)
            models.Booking.objects.bulk_create(add_booking_list)

            remove_booking = Q()
            for room_id, time_id_list in booking_info['del'].items():
                for time_id in time_id_list:
                    temp = Q()
                    temp.connector = 'AND'
                    temp.children.append(('user_id', request.session['user_info']['id'],))
                    temp.children.append(('booking_date', booking_date,))
                    temp.children.append(('room_id', room_id,))
                    temp.children.append(('booking_time', time_id,))
                    remove_booking.add(temp, 'OR')
            if remove_booking:
                models.Booking.objects.filter(remove_booking).delete()
        except IntegrityError as e:
            ret['code'] = 1011
            ret['msg'] = '会议室已被预定'

        except Exception as e:
            ret['code'] = 1012
            ret['msg'] = '预定失败：%s' % str(e)

    return JsonResponse(ret)
```

###### 前端渲染

```javascript
<script>

    // 对Date的扩展，将 Date 转化为指定格式的String
    // 月(M)、日(d)、小时(h)、分(m)、秒(s)、季度(q) 可以用 1-2 个占位符，
    // 年(y)可以用 1-4 个占位符，毫秒(S)只能用 1 个占位符(是 1-3 位的数字)
    // 例子：
    // (new Date()).Format("yyyy-MM-dd hh:mm:ss.S") ==> 2006-07-02 08:09:04.423
    // (new Date()).Format("yyyy-M-d h:m:s.S")      ==> 2006-7-2 8:9:4.18
    Date.prototype.Format = function (fmt) { //author: meizz
        var o = {
            "M+": this.getMonth() + 1, //月份
            "d+": this.getDate(), //日
            "h+": this.getHours(), //小时
            "m+": this.getMinutes(), //分
            "s+": this.getSeconds(), //秒
            "q+": Math.floor((this.getMonth() + 3) / 3), //季度
            "S": this.getMilliseconds() //毫秒
        };
        if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
        for (var k in o)
            if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        return fmt;
    };

    SELECTED_ROOM = {del: {}, add: {}};
    CHOSEN_DATE = new Date().Format('yyyy-MM-dd');

    $(function () {
        initDatepicker();
        initBookingInfo(new Date().Format('yyyy-MM-dd'));
        bindTdEvent();
        bindSaveEvent();
    });

    function csrfSafeMethod(method) {
        // these HTTP methods do not require CSRF protection
        return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
    }
    $.ajaxSetup({
        beforeSend: function (xhr, settings) {
            if (!csrfSafeMethod(settings.type) && !this.crossDomain) {
                xhr.setRequestHeader("X-CSRFToken", $.cookie('csrftoken'));
            }
        }
    });

    function initDatepicker() {
        $('#datetimepicker11').datetimepicker({
            minView: "month",
            language: "zh-CN",
            sideBySide: true,
            format: 'yyyy-mm-dd',
            bootcssVer: 3,
            startDate: new Date()
            //autoclose: true,
        }).on('changeDate', changeDate);
    }

    function changeDate(ev) {
        CHOSEN_DATE = ev.date.Format('yyyy-MM-dd');
        initBookingInfo(CHOSEN_DATE);

    }

    function initBookingInfo(date) {
        SELECTED_ROOM = {del: {}, add: {}};

        $('#shade,#loading').removeClass('hide');
        $.ajax({
            url: '/booking/',
            type: 'get',
            data: {date: date},
            dataType: 'JSON',
            success: function (arg) {
                $('#shade,#loading').addClass('hide');
                if (arg.code === 1000) {
                    $('#tBody').empty();
                    $.each(arg.data, function (i, item) {
                        var tr = document.createElement('tr');
                        $.each(item, function (j, row) {
                            var td = document.createElement('td');
                            $(td).text(row.text);

                            $.each(row.attrs, function (k, v) {
                                $(td).attr(k, v);
                            });
                            if (row.chosen) {
                                $(td).addClass('chosen');
                            }
                            $(tr).append(td)
                        });
                        $('#tBody').append(tr);
                    })
                } else {
                    alert(arg.msg);
                }
            },
            error: function () {
                $('#shade,#loading').addClass('hide');
                alert('请求异常');
            }
        })
    }

    /*
     绑定预定会议室事件
     */
    function bindTdEvent() {
        $('#tBody').on('click', 'td[time-id][disable!="true"]', function () {

            var roomId = $(this).attr('room-id');
            var timeId = $(this).attr('time-id');

            //var item = {'roomId': $(this).attr('room-id'), 'timeId': $(this).attr('time-id')};

            if ($(this).hasClass('chosen')) {
                $(this).removeClass('chosen').empty();
                // 取消原来的预定
                //SELECTED_ROOM['del'].push(item);
                if (SELECTED_ROOM.del[roomId]) {
                    SELECTED_ROOM.del[roomId].push(timeId);
                } else {
                    SELECTED_ROOM.del[roomId] = [timeId];
                }

            } else if ($(this).hasClass('selected')) {
                $(this).removeClass('selected');
                // 取消选择
                var timeIndex = SELECTED_ROOM.add[roomId].indexOf(timeId);
                if (timeIndex !== -1) {
                    SELECTED_ROOM.add[roomId].splice(timeIndex, 1);
                }
            } else {
                $(this).addClass('selected');
                // 选择
                if (SELECTED_ROOM.add[roomId]) {
                    SELECTED_ROOM.add[roomId].push(timeId);
                } else {
                    SELECTED_ROOM.add[roomId] = [timeId];
                }
            }
        })
    }

    /*
     保存按钮
     */
    function bindSaveEvent() {
        $('#errors').text('');

        $('#save').click(function () {
            $('#shade,#loading').removeClass('hide');
            $.ajax({
                url: '/booking/',
                type: 'POST',
                data: {date: CHOSEN_DATE, data: JSON.stringify(SELECTED_ROOM)},
                dataType: 'JSON',
                success: function (arg) {
                    $('#shade,#loading').addClass('hide');
                    if (arg.code === 1000) {
                        initBookingInfo(CHOSEN_DATE);
                    } else {
                        $('#errors').text(arg.msg);
                    }
                }
            });
        });

    }


</script>

```