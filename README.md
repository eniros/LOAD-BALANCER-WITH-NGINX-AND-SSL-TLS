# LOAD-BALANCER-WITH-NGINX-AND-SSL-TLS
Configure Nginx As A Load Balancer

Step 1 - Create an Nginx WebServer which will be configured as loadbalancer 

<img width="821" alt="Screenshot 2022-12-02 at 11 58 35" src="https://user-images.githubusercontent.com/61475969/205288001-c7ffd0d6-8692-4e03-a446-5de895e2a9fd.png">

Update /etc/hosts file for local DNS with Web Servers names (e.g. Web1 and Web2) and their local IP addresses just like it was done with the apache load balancer.

<img width="568" alt="Screenshot 2022-12-02 at 12 04 33" src="https://user-images.githubusercontent.com/61475969/205289371-25344e80-e23d-4874-ace0-7ec2a5ed1f5f.png">

<img width="563" alt="Screenshot 2022-12-02 at 12 21 20" src="https://user-images.githubusercontent.com/61475969/205291891-a4f77cac-76a0-40f7-afc5-54a8be8a4b4a.png">


Configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

<img width="563" alt="Screenshot 2022-12-02 at 12 19 35" src="https://user-images.githubusercontent.com/61475969/205291664-1331f96b-2ae7-44e7-a091-10a85b9054d6.png">


To test that nginx has been install, I browsed the public IP of the EC2 instance

<img width="900" alt="Screenshot 2022-12-02 at 12 18 12" src="https://user-images.githubusercontent.com/61475969/205291370-60d7e4b7-897c-4f74-80ce-d472cbb4eff3.png">

Edit the Nginx config file by running the command:

sudo vi /etc/nginx/nginx.conf

Insert the configuration script into the http section of the config file
<img width="563" alt="Screenshot 2022-12-02 at 12 27 12" src="https://user-images.githubusercontent.com/61475969/205293234-e85b729f-a0e5-4287-ac15-a59e45e748d0.png">

Step 2REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES

Register a new domain name with any registrar

Assign an Elastic IP to Nginx Load Balancer server and associated domain name with the Elastic IP.

Scroll to the networking section in the action tab on your instance page and click on the 'Manage IP Address' button

Click on the 'Allocate' link on the page, Allocate the Elastic IP address on the next page

<img width="843" alt="Screenshot 2022-12-02 at 12 35 44" src="https://user-images.githubusercontent.com/61475969/205294614-5e766eca-6fba-4e9a-81ad-cc094c01c106.png">

<img width="1011" alt="Screenshot 2022-12-02 at 12 40 07" src="https://user-images.githubusercontent.com/61475969/205295426-4f39b4de-0f87-4310-9194-8fdaebf8236b.png">

Select the Elastic IP address to associate and choose Actions, Associate Elastic IP address.

For Resource type, choose Instance.

For instance, choose the instance with which to associate the Elastic IP address. You can also enter text to search for a specific instance.

(Optional) For Private IP address, specify a private IP address with which to associate the Elastic IP address.

Choose Associate. 

<img width="1069" alt="Screenshot 2022-12-02 at 12 45 44" src="https://user-images.githubusercontent.com/61475969/205296109-e1c73560-0fd8-4d34-afd1-04374b41d1d3.png">

<img width="1069" alt="Screenshot 2022-12-02 at 12 47 10" src="https://user-images.githubusercontent.com/61475969/205296284-c73308d0-fd93-4b42-83fd-c167b2abfbd1.png">


Update A record in your registrar to point to Nginx LB using Elastic IP address

Configure Nginx to recognize the new domain name. This was done by Updating the /etc/nginx/nginx.conf file with

server_name www.<your-domain-name.com>
instead of server_name www.domain.com

Install certbot and request for an SSL/TLS certificate for the domain name. N.B: Make sure snapd is running on the server.

sudo systemctl status snapd

<img width="573" alt="Screenshot 2022-12-02 at 12 50 46" src="https://user-images.githubusercontent.com/61475969/205296904-7b902e0e-0e46-4ff3-8547-d13eead53681.png">

<img width="561" alt="Screenshot 2022-12-02 at 12 53 01" src="https://user-images.githubusercontent.com/61475969/205297311-ac84cf23-c559-4803-9553-23d0472332bc.png">

sudo snap install --classic certbot

<img width="561" alt="Screenshot 2022-12-02 at 12 54 10" src="https://user-images.githubusercontent.com/61475969/205297557-c6dba83b-7558-4607-8e93-6b349899f46d.png">

Make a Request your certificate for the domain name.
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
Follow the instruction displayed

Lets Encrypt renews every 90 days and you can renew your certificate manually by running the following command.
sudo certbot renew --dry-run
We can also create a cron job to do this same thing at a stipulated time.

Edit cron file
crontab -e
Add the following line to the crontab file
5 */12 * */2 *   root /usr/bin/certbot renew > /dev/null 2>&1
Save the crontab file
