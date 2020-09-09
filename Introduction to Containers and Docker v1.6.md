# LAB:Introduction to Containers and Docker v1.6 : Run and Distribute Containers With Docker


# Objectives 

In this lab, you will learn to perform the following tasks;
- Build a Docker image
- Push a Docker image to Google Cloud Registry
- Run a Docker container

## Steps:

1.  Create a Compute Engine virtual machine using the gcloud command-line interface.

       
       gcloud config set compute/Zone us-central1-f

       
       gcloud compute instances create k8s-workshop-module-1-lab --machine-type "n1-standard-1" --image-project "ubuntu-cloud" --image "ubuntu-1604-xenial-v20190212" --subnet "default"

2.  Create disk
   
       
       gcloud compute disks create module-1-lab
       gcloud compute disks create module-1-lab \
       --size 10G \
       --type pd-ssd \
       --region us-central1-f \
       --replica-zones us-central1-c,us-central1-e

3.  Attach disk to instance

       
       gcloud compute instances attach-disk k8s-workshop-module-1-lab \ --disk module-1-lab


4.  ssh into vm k8s-workshop-module-1-lab
 

       - use the ssh command to open a command prompt


                ssh k8s-workshop-module-1-lab


5.  Run the Web Server Manually


       -  Switch to directory where we have the source code.

            
            cd /kickstart  

       
       -  List the content

  
            ls -lh


       -  Install dependencies and Install the latest version of Python and PIP.

    
            
            sudo apt-get install -y python3 python3-pip

        
       
       -  Install Tornado library that is required by the application.


            
            pip3 install tornado


       
       -  Run the Python application in the background.


            
            python3 web-server.py &


      
       -  To ensure that the web server is accessible.


            curl http://localhost:8888


       
       -  Terminate the web server.


             kill %1


6. Package Using Docker


       -  Look at the Dockerfile.

             
             
             cat Dockerfile

       
      
       -  Build a Docker image with the web server.

             
             
             sudo docker build -t py-web-server:v1 


       
       -  Run the web server using Docker.


             
             sudo docker run -d -p 8888:8888 --name py-web-server -h my-web-server py-web-server:v1


       
       -  Try accessing the web server again

                 
             
             curl http://localhost:8888


      
       -  Stop the container

  

             sudo docker rm -f py-web-server



7.  Upload the Image to a Registry


       
       -  Add the signed in user to the Docker group and push the image to the repository as an authenticated user


             
              sudo usermod -aG docker $USER



       -  Exit the current SSH session


              exit


       -  launch a new SSH session



              ssh k8s-workshop-module-1-lab


 
       -   return to the kickstart directory.



              cd /kickstart


       -   Store your GCP project name in an environment variable.



              export GCP_PROJECT=`gcloud config list core/project --format='value(core.project)'`


      
       -   Rebuild the Docker image with a tag that includes the registry name gcr.io and the project ID as a prefix.




              docker build -t "gcr.io/${GCP_PROJECT}/py-web-server:v1"




8.   Make the Image Publicly Accessible



       -   Configure Docker to use gcloud as a Container Registry credential helper, When prompted, press ENTER.



              PATH=/usr/lib/google-cloud-sdk/bin:$PATH 
              gcloud auth configure-docker



       -   Push the image to gcr.io.



              docker push gcr.io/${GCP_PROJECT}/py-web-server:v1



       -   Update the permissions on Google Cloud Storage to make your image repository publicly accessible.



              gsutil defacl ch -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com


              gsutil acl ch -r -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com


              gsutil acl ch -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com



9.   Run the Web Server From Any Machine



       -   The Docker image can now be run from any machine that has Docker installed by running the following command.



              docker run -d -p 8888:8888 -h my-web-server gcr.io/${GCP_PROJECT}/py-web-server:v1   





       


       
       
