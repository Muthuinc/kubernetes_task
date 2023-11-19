### Setting up jenkins server   

#### Task 1 Verify jenkins installation  
 - followed the official jenkins installation for ubuntu [clickhere](https://pkg.jenkins.io/debian-stable/)  
  ![pic1](./pictures/jenkins1.png)
 - Jenkins installed successfully.  


 #### Task 2 CI Pipeline Execution

  - In the github repository webhook set up is by the jenkins url  
  ![pic2](./pictures/webhook.png).
  - You can see it is reachable.  
  - In the jenkins dashboard a Pipeline job created with auto-build trigger from github.
  ![pic3](./pictures/webhookset.png)
  - After pushing the files from the local github sent push trigger to the jenkins. 
  ![pic4](./pictures/push.png).
  - CI part successfull.
  ![pic5](./pictures/ci.png)


#### Task 3 CD Pipeline and Deployment

 - For CD Nginx is installed in the ec2 machine.
  ![pic6](./pictures/bd.png)
 - Jenkins job has been triggered successfully.
 ![pic7](./pictures/fd.png)
 - Few issues with the permission. but finally it came success. As a result new webpage is deployed in the nginx server.
 ![pic8](./pictures/ad.png)

##### All the tasks has been successfully completed.
