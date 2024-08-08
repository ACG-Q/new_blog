# 开启VNC

## 1. 打开树莓派配置

```shell
sudo raspi-config
```

![image](https://github.com/user-attachments/assets/ec7cc746-0f42-4113-a34c-12a79d74dabe)

## 2. 选择`3 Interface Options    Configure connections to peripherals`并按下回车

![image](https://github.com/user-attachments/assets/9863a1b4-66df-4254-8e28-f02f9c85dd29)

## 3. 选择`I3 VNC         Enable/disable graphical remote desktop access `并按下回车

![image](https://github.com/user-attachments/assets/86e38a57-8592-4af3-940f-881ee46a3309)

## 4. 选择`Yes`并按下回车x2

![image](https://github.com/user-attachments/assets/0ff365eb-aa19-44b1-89bc-911e4274e044)

![image](https://github.com/user-attachments/assets/63c6adff-f007-4e06-a7dd-a63715355c9f)

![image](https://github.com/user-attachments/assets/ca6ae887-f2ce-4e2c-ace6-b0e93e1d0428)

## 5. 选择`Finsh`并按下回车

![image](https://github.com/user-attachments/assets/dccd4ae5-24be-4575-b141-9697ed5e9969)

## 6. 完成

> 正常情况下，菜单栏右边会出现这个图标

![image](https://github.com/user-attachments/assets/c09b5460-3a75-4d27-a7be-c9933a6c36b8)


# 遇到的问题

## 1. No configured security type is supported by 3.3 VNC Viewer

![image](https://github.com/user-attachments/assets/f7c71009-e46c-4ab8-8837-783e1ccc6dd9)

![image](https://github.com/user-attachments/assets/9ca920b5-9da7-4a71-84b5-665d1213751e)

> 由于新版树莓派镜像更换了RealVNC
> 所以导致非RealVNC查看器无法正常连接树莓派

### 方案1: 关闭VNC验证

1. 添加或者追加以下内容到`/etc/vnc/config.d/common.custom`文件
```text
# no encryption
SecurityTypes=None
```
2. 运行`sudo systemctl restart vncserver-x11-serviced.service`

### 方案2: 允许来自旧版 VNC 查看器的连接

#### 实现方法: 通过命令行

```shell
sudo su
echo 'Encryption=PreferOn' | sudo tee -a /root/.vnc/config.d/vncserver-x11
sudo vncpasswd -legacy -service
sudo systemctl restart vncserver-x11-serviced
```

#### 通过GUI界面开启

![image](https://github.com/user-attachments/assets/800804dc-190b-4ef9-993d-c17cc5b373ad)


## 2. Failed when connecting: Connection closed (code: 1006)

解决方案: https://github.com/novnc/noVNC/issues/1438#issuecomment-664627093

## 3. 

