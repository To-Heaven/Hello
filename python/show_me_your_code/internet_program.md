# 网络编程
## socket套接字实现简单ftp
- 服务端

```python
# 服务端
import socket
import struct
import json
import os


class MyTCPServer:
    # 相关配置变量设置为类属性
    adress_family = socket.AF_INET
    socket_type = socket.SOCK_STREAM
    allow_reuse_adress = False
    max_packet_size = 1024
    coding = 'utf-8'
    request_queue_size = 5
    server_dir = 'server_download'

    def __init__(self, server_address, bind_and_activate=True):
        '''
        初始化对象
        :param server_address: 服务端address
        :param bind_and_activate: 是否绑定并监听客户端request
        '''
        self.server_address = server_address
        self.socket = socket.socket(self.adress_family,
                                    self.socket_type)
        if bind_and_activate:
            try:
                self.server_bind()
                self.server_activate()
            except:
                self.server_close()
                # raise

    def server_bind(self):
        '''
        给服务端绑定address
        :return: None
        '''
        if self.allow_reuse_adress:
            self.socket.setsockopt(socket.SOL_SOCKET,
                                   socket.SO_REUSEADDR, 1)
        self.socket.bind(self.server_address)

    def server_activate(self):
        '''
        实现服务端监听
        :return:None
        '''
        self.socket.listen(self.request_queue_size)

    def server_close(self):
        '''
        关闭服务端套接字
        :return: None
        '''
        self.socket.close()

    def get_request(self):
        '''
        接收客户端的请求
        :return: 元组，(client_ip, client_port)
        '''
        return self.socket.accept()

    def close_request(self, request):
        '''
        关闭与客户端之间的链接
        :param request: 客户端address
        :return: None
        '''
        request.close()

    def put(self, args):
        '''
        实现下载功能
        :param args: 报头字典
        :return: None
        '''
        file_path = os.path.normpath(
            os.path.join(
                self.server_dir,
                args['filename']
            ))
        file_size = args['filesize']
        recv_size = 0
        with open(file_path, 'wb') as fb:
            while recv_size < file_size:
                recv_data = self.conn.recv(self.max_packet_size)
                fb.write(recv_data)
                recv_size += len(recv_data)
            else:
                print('download successful')

    def run(self):
        '''
        主程序，实现建立链接，并下载功能
        :return:None
        '''
        while True:
            try:
                self.conn, self.client_address = self.get_request()
                while True:
                    head_struct = self.conn.recv(4)
                    if not head_struct:break
                    head_length = struct.unpack('i', head_struct)[0]
                    head_json = self.conn.recv(head_length).decode(self.coding)
                    head_dic = json.loads(head_json)
                    # head_dic = {'cmd':'put', 'filename':'a.txt', 'filesize':1231456}
                    cmd = head_dic['cmd']
                    if hasattr(self, cmd):
                        task = getattr(self, cmd)
                        task(head_dic)
            except Exception:
                break
```

- 客户端

```
# 客户端
import  socket
import struct
import json
import os

class MyTCPClient:
    address_family = socket.AF_INET
    socket_type = socket.SOCK_STREAM
    max_packet_size = 1024
    coding = 'utf-8'

    def __init__(self, server_address, connect=True):
        self.server_address = server_address
        self.socket = socket.socket(self.address_family,
                                    self.socket_type)
        if connect:
            pass

    def client_conect(self):
        '''
        向客户端发送链接请求
        :return: None
        '''
        self.socket.connect(self.server_address)

    def client_close(self):
        '''
        关闭双向链接
        :return: None
        '''
        self.socket.close()

    def run(self):
        '''
        主程序，交互式获取将要发送的文件和要执行的命令
        :return: None
        '''
        while True:
            msg = input('>>>').strip()
            if not msg: continue
            task_list = msg.split()
            cmd = task_list[0]
            if hasattr(self, cmd):
                task = getattr(self, cmd)
                task(task_list)

    def put(self, args):
        '''
        执行文件发送功能
        :param args: 命令列表，包括命令和文件名
        :return: None
        '''
        cmd = args[0]
        file_path = args[1]
        if not os.path.isfile(file_path):
            print('file: %s if not exists'%file_path)
            return
        else:
            file_size = os.path.getsize(file_path)

        head_dic = {
            'cmd':cmd,
            'filename':os.path.basename(file_path),
            'filesize':file_size
        }
        head_json = json.dumps(head_dic)
        head_json_bytes = bytes(head_json, encoding=self.coding)
        head_struct= struct.pack('i', len(head_json_bytes))
        self.socket.send(head_struct)
        self.socket.send(head_json_bytes)
        send_size = 0
        with open(file_path, 'rb') as lines:
            for line in lines:
                self.socket.send(line)
                send_size += len(line)
            else:
             print('upload successful')
```