# LAB: Google Cloud Fundamentals: Getting Started with Compute Engine

## Objectives: 
In this lab, you will learn how to perform the following tasks:

    -Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

    -Create a Compute Engine virtual machine using the gcloud command-line interface.

    -Connect between the two instances.

## Before started , note that all commands used there are based on my lab (such as my project name , project ID etc.), you must use yours too.   

## Steps:

1. Create a virtual machine using the GCP Console 

    gcloud beta compute --project=qwiklabs-gcp-04-ebac082f87a6 instances create my-vm-1 --zone=us-central1-a --machine-type=e2-medium --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=720165624287-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=my-vm-1 --reservation-affinity=any

    gcloud compute --project=qwiklabs-gcp-04-ebac082f87a6 firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

2. Create a virtual machine using the gcloud command line

    *To display a list of all the zones in the region 
        gcloud compute zones list | grep us-central1

    *To set your default zone to the one you just chose
        gcloud config set compute/zone us-central1-b

    *To create a VM sudo nano /var/www/html/index.nginx-debian.htmlinstance called my-vm-2 in that zone
        gcloud compute instances create "my-vm-2" \
        --machine-type "n1-standard-1" \
        --image-project "debian-cloud" \
        --image "debian-9-stretch-v20190213" \
        --subnet "default"
    
3. Connect between VM instances

    *Use the ping command to confirm that my-vm-2 can reach my-vm-1 over the network
        ping -c 4 my-vm-1

    *Use the ssh command to open a command prompt on my-vm-1
        ssh my-vm-1
    *install the Nginx web server on my-vm-1
        sudo apt-get install nginx-light -y

    *Use the nano text editor to add a custom message to the home page of the web server
        sudo nano /var/www/html/index.nginx-debian.html

    *Use the arrow keys to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name
        Hi from MY_NAME

    *Confirm that the web server is serving your new page. At the command prompt on my-vm-1, execute this command
        curl http://localhost/
    *result: The response will be the HTML source of the web server's home page, including your line of custom text

    *To exit the command prompt on my-vm-1, execute this command
        exit

    *To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command 
        curl http://my-vm-1/
    *result: The response will again be the HTML source of the web server's home page, including your line of custom text

    *Copy the External IP address for my-vm-1 and paste it into the address bar of a new browser tab. You will see your web server's home page, including your custom text