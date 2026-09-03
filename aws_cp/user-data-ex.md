###copy everything below this line to transform an instance to a httpd/apache server which can be accessed over http or https with ssl certificate

#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</n1>" >/var/www/html/index.html
