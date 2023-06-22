# Python ENV Management
1. Create virtual enviroment
``` Terminal
python -m venv jonny
```
2. Go to your enviroment directory
``` Terminal
cd jonny
```
3. Change execution policy
``` Terminal
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
4. Activate python enviroment for Windows
``` Terminal
.\Scripts\activate
```
5. Activate python enviroment for MacOS
``` Terminal
source jonny/bin/activate
```
6.  Check if any python package installed
``` Terminal
pip freeze
```
7. Install flask
``` Terminal
pip install flask
```
8. Deactivate python enviroment
``` Terminal
deactivate
```
# Deploy Flask on AWS EC2
1. Update Amazon Linux
``` Terminal
sudo yum update
sudo yum upgrade
```
2. Install python
``` Terminal
sudo yum install python3.11
```
3. Check python version
``` Terminal
python3 --version
```
4. Create the virtual environment
``` Terminal
python3 -m venv myEnv
```
5. Activate the virtual environment
``` Terminal
source venv/bin/activate
```
6. Install flask
``` Terminal
pip install flask
```
7. Create a simple Flask API
``` Terminal
sudo vim app.py
```
1. Add this to app.py
``` Python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
	return 'Hello World!'

if __name__ == "__main__":
	app.run()
```
8. Verify if it works by running
``` Terminal
python3 app.py
```
9. Install gunicorn
``` Terminal
pip install gunicorn
``` 
10. Run Gunicorn
``` Terminal
gunicorn -b 0.0.0.0:8000 app:app 
```
11. Change directory
``` Terminal
cd /etc/systemd/system/
```
12. Create config for flask API
``` Terminal
touch helloworld.service
```
13. Then add this into the file
```Terminal
[Unit]
Description=Gunicorn instance for a simple hello world app
After=network.target
[Service]
User=ec2-user
Group=ec2-user
WorkingDirectory=/home/ec2-user/helloworld
ExecStart=/home/ec2-user/helloworld/myEvn/bin/gunicorn -b localhost:8000 app:app
Restart=always
[Install]
WantedBy=multi-user.target
```
14. Enable and start the service
``` Terminal
sudo systemctl daemon-reload
sudo systemctl enable helloworld.service
sudo systemctl start helloworld.service
```
15. sudo systemctl status helloworld.service
``` Terminal
sudo systemctl status helloworld.service
```
16. Check if the app is running
``` Terminal
curl localhost:8000
```
17. Start and enable the Nginx service and go to the Public IP address of your EC2
``` Terminal
sudo systemctl start nginx
sudo systemctl enable nginx
```
18. Change directory to config file
``` Terminal
cd /etc/nginx/conf.d/
```
19. Edit the config the file to bridging flask and nginx
``` Terminal
vim jonny.conf
```
20. Add the following code at the top of the file
```Terminal
http {
    # ...

    upstream flaskhelloworld {
        server 127.0.0.1:8000;
    }

    # ...
}
```
21. Add a proxy_pass to flaskhelloworld atlocation
``` Terminal
server {
    # ...

    location / {
        proxy_pass http://flaskhelloworld;
    }

    # ...
}
```
22. Restart nginx
``` Terminal
sudo systemctl restart nginx
```