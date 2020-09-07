# LAB: Getting Started with Cloud Storage and Cloud SQL

## Objectives 

In this lab, you learn how to perform the following tasks:

    -Create a Cloud Storage bucket and place an image into it.

    -Create a Cloud SQL instance and configure it.

    -Connect to the Cloud SQL instance from a web server.

    -Use the image in the Cloud Storage bucket on a web page.

## Steps

1. Deploy a web server VM instance

    -create VM instance with startup script

        gcloud beta compute --project=qwiklabs-gcp-04-bec0925c1e41 instances create bloghost --zone=us-central1-a --machine-type=e2-medium --subnet=default --network-tier=PREMIUM --metadata=startup-script=apt-get\ update$'\n'apt-get\ install\ apache2\ php\ php-mysql\ -y$'\n'service\ apache2\ restart --maintenance-policy=MIGRATE --service-account=206699583808-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=bloghost --reservation-affinity=any

gcloud compute --project=qwiklabs-gcp-04-bec0925c1e41 firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server



2. Create a Cloud Storage bucket using the gsutil command line

    -Start Cloud Shell

    -enter chosen location into an environment variable called LOCATION

        export LOCATION=US
    
    -Enter this command to make a bucket named after your project ID:

        gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID
    
    -Retrieve a banner image from a publicly accessible Cloud Storage location:

        gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png

    -Copy the banner image to your newly created Cloud Storage bucket:

        gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

    -Modify the Access Control List of the object you just created so that it is readable by everyone:

        gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

3. Create the Cloud SQL instance

    -use the command below to create the sql instance

        gcloud sql instances create blog-db --password="blog-db" --sql-version=MYSQL_5_7" --zone=us-central1-a

    -use the command below to add user

        gcloud sql users create blogdbuser --instance=blog-db -i blog-db --host='%' --password='blogdbuser'

    -authorize bloghost vm :

        gcloud compute instances list
        gcloud sql instances patch blog-db --authorized-networks="23.236.49.31/32"

4. Configure an application in a Compute Engine instance to use Cloud SQL

    -In the VM instances list, click SSH in the row for your VM instance bloghost.

    -In your ssh session on bloghost, change your working directory to the document root of the web server:

        cd /var/www/html

    -Use the nano text editor to edit a file called index.php:

        sudo nano index.php

    -Paste the content below into the file:

        <html>
        <head><title>Welcome to my excellent blog</title></head>
        <body>
        <h1>Welcome to my excellent blog</h1>
        <?php
        $dbserver = "CLOUDSQLIP";
        $dbuser = "blogdbuser";
        $dbpassword = "DBPASSWORD";
        // In a production blog, we would not store the MySQL
        // password in the document root. Instead, we would store it in a
        // configuration file elsewhere on the web server VM instance.

        $conn = new mysqli($dbserver, $dbuser, $dbpassword);

        if (mysqli_connect_error()) {
                echo ("Database connection failed: " . mysqli_connect_error());
        } else {
                echo ("Database connection succeeded.");
        }
        ?>
        </body></html>
    
    -Press Ctrl+O, and then press Enter to save your edited file.

    -Press Ctrl+X to exit the nano text editor.

    -Restart the web server:

        sudo service apache2 restart

    -Open a new web browser tab and paste into the address bar your bloghost VM instance's external IP address followed by /index.php. The URL will look like this:

        35.192.208.2/index.php
    
    -When you load the page, you will see that its content includes an error message beginning with the words:

        Database connection failed: ...
    
    -Return to your ssh session on bloghost. Use the nano text editor to edit index.php again.

        sudo nano index.php

    -In the nano text editor, replace CLOUDSQLIP with the Cloud SQL instance Public IP address that you noted above. Leave the quotation marks around the value in place.

    -In the nano text editor, replace DBPASSWORD with the Cloud SQL database password that you defined above. Leave the quotation marks around the value in place.

    -Press Ctrl+O, and then press Enter to save your edited file.

    -Press Ctrl+X to exit the nano text editor.

    -Restart the web server:

        sudo service apache2 restart
        
    -Return to the web browser tab in which you opened your bloghost VM instance's external IP address. When you load the page, the following message appears:

        Database connection succeeded.

5. Configure an application in a Compute Engine instance to use a Cloud Storage object

    -In the GCP Console, click Storage > Browser.

    -Click on the bucket that is named after your GCP project.

    -In this bucket, there is an object called my-excellent-blog.png. Copy the URL behind the link icon that appears in that object's Public access column, or behind the words "Public link" if shown.

    -Return to your ssh session on your bloghost VM instance.

    -Enter this command to set your working directory to the document root of the web server:

        cd /var/www/html

    -Use the nano text editor to edit index.php:

        sudo nano index.php

    Use the arrow keys to move the cursor to the line that contains the h1 element. Press Enter to open up a new, blank screen line, and then paste the URL you copied earlier into the line.

    -Paste this HTML markup immediately before the URL:

        <img src='

    -Place a closing single quotation mark and a closing angle bracket at the end of the URL:

        '>

    -The resulting line will look like this:

        <img src='https://storage.googleapis.com/qwiklabs-gcp-0005e186fa559a09/my-excellent-blog.png'>

    -Press Ctrl+O, and then press Enter to save your edited file.

    -Press Ctrl+X to exit the nano text editor.

    -Restart the web server:

        sudo service apache2 restart

    -Return to the web browser tab in which you opened your bloghost VM instance's external IP address. When you load the page, its content now includes a banner image


    

                        