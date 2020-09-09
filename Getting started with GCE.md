# LAB: Google Cloud Fundamentals : Getting started with Compute Engine

##Objectives

In this lab, you will learn to perform the following tasks;

    - Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) console.
    
    - Create a Compute Engine virtual machine using the gcloud command-line interface.

    - Connect between the two instances.

## Steps:

1. Create a Compute Engine Virtual machine using the Cloud Platform (GCP) Console.

       gcloud compute instances create my-vm-1 --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --tags http

       gcloud compute firewall-rules create allow-http --action=ALLOW --destination=INGRESS --rules=http:80 --target-tags=http



2. Create a Compute Engine virtual machine using the gcloud command-line interface.

       gcloud config set compute/Zone us-central1-b

       gcloud compute instances create my-vm-2 --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"


3. Connect between the two instances.

   1. Use the ping command that my-vm-2 can reach my-vm-1 over the network:

      - connect to  my-vm-2:
    
              gcloud compute ssh my-vm-2

      - ping my-vm-1 from my-vm-2:
  
              ping -c 4 my-vm-1
   
      - use the ssh command to open a command prompt on my-vm-1 from my-vm-2:

              ssh my-vm-1

      At the command prompt my-vm-1, install the nginx web browser:

              sudo apt-get install nginx-light -y

      - use the nano text editor to add a custom message to the home page of the web server:

              sudo nano /var/www/html/index.nginX-debian.html

      - add text file like this, and replace YOUR_NAME with your name:

              Hi from YOUR_NAME

      - exit the editor and confirm that the web server is serving your new page. 

   At the command prompt on my-vm-1, execute this command:

              curl http://localhost/		
 
   To exit the command prompt on my-vm-1, execute this command:

              exit

   Return to the command prompt on my-vm-2, To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command:

              curl http://my-vm-1/