# FTP

Connect to the server remotely and upload files

## Get public ip

[Get public ip](https://www.whatismyip.com/)

- Enter the website and see what's your ps's public ip
- Do not open VPN
![](publicip.png)


## ECS

[Security Group Detail](https://ecs.console.aliyun.com/securityGroupDetail/region/)

![ecsse.png](ecsse.png)

- Click ![image_1dd.png](image_1dd.png){width="57"}
![add.png](add.png)
- Fill the `port range` with **1/65535** and type the `Authorized object` with public ip address
- Can refer to the screenshot below
![image_sdfds1.png](image_sdfds1.png)
- Click `save`
- When you're done, remove the ip from the security group

## MobaXterm

[Download](https://mobaxterm.mobatek.net/download-home-edition.html)

### Home page
![mobaxterm.png](mobaxterm.png)

## Connect

- Click the `Session` ![session.png](session.png){width="25"} and click `SSH`![ssh.png](ssh.png){width="20"}
![session_settings.png](session_settings.png)
- Enter the server public ip address in `Remote host`
- Check `Specify username` and fill `Port`,then click `OK`
![r_host.png](r_host.png)
- When connecting for the first time, you will be prompted to enter the password, enter it and press enter
![login.png](login.png)

## Upload
- Our resources located at `/data/`,type in the input field and press enter
- When we want to upload resources,click `upload`  ![upload_b.png](upload_b.png){width="20"}
![upload.png](upload.png)
![data.png](data.png)
![upload_button.png](upload_button.png)

## FileZilla

[Download](https://download.filezilla.cn/client/windows/FileZilla_3.66.5_win64.zip)

### I recommend using this upload model.

### Home

![FileZilla.png](FileZilla.png)

- Fill the Host with : `sftp://xx.xx.xx.xx`
- **Note that you should add `sftp://` before the ip**
- Click `Edit` -> `Settings...`
![imagesett.png](imagesett.png)
- Choose the actice mode
![image.png](image.png)
- And then click `Quickconnect`
- Drag resources into target directory,such as upload these models into right highlight directory.
![uploadmodels.png](uploadmodels.png)
- You did it!

