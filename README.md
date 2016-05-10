#Following are steps to be followed after launching AMI :
1.Create an instance using AMI IONICApplication-Salesforce -Jenkins in Sao Paulo region.

2.Now have to check whether jenkins is up and running.Open 
                          <ipaddress>:8080
Jenkins dashboard will be opened now we have to manage plugins.Following are the plugins that are to be installed

3.Goto dashboard-> manage jenkins->manage plugins->available (install without restart)
  
 # - Github Plugin
 # - Docker plugin
  
- Open a terminal or create a job to check what images are present and containers are running : 


      docker images
      
      docker ps
      

  # CONTINOUS INTEGRATION IN JENKINS 
 
5.Now our requirement is this job contains a sample ionic application which is done using dockerfiles so if any change is happened to the code jenkins should help us to trigger (refer note below) and update our application.Following are the steps to be followed :
  
      A. Create a job in jenkins for example FINAL JOB.
      
      B. Now configure in source code management select git :
           a. Add repositories where our sample ionic application is present and dockerfile
                - https://github.com/samhitha30/ionic-app.git 
                - https://github.com/SusrithaMunukutla/ionic-sensorapp.git
           b. Add credentails by clicking Add -> Include username,password,description .
           
      C. Now in Build trigger select Build when a change is pushed to GitHub, when any changes made to our git repository and commit,jenkins helps to trigger and build job with updated code.
     
      D. Now in Build -> Add build step -> Execute Shell
          a. Before executing this job make sure no container is running.
          
                  docker run -i -d -p 8100:8100 --entrypoint=/entrypoint.sh <image-name> -s
      
      E. Now save the job and build 
                       or
         Make any changes in your git repository and commit job gets triggered and builds.
           (a) Now if you have executed job then kill and remove ionic container then run container orelse you will get error rename container:
                docker kill <image-name>
                docker rm -f <image-name>
                docker run -i -d -p 8100:8100 --entrypoint=/entrypoint.sh <image-name> -s
                
  # CONTINOUS DELIVERY IN JENKINS
      
      1. Created seperate jobs for all containers : ionic_application , couch_database, sync_gateway, couchbase , talend 
      2. Now this jobs will be executed only when any change happened to dockerfile ie triggers and then executes others jobs .
         -> Following are steps followed in jobs :
            A. Now configure in source code management select git :
                   a. Add repositories where our sample ionic application is present and dockerfile
                        - https://github.com/SusrithaMunukutla/ionic-sensorapp.git
                   b. Add credentails by clicking Add -> Include username,password,description .
           
           B. Now in Build trigger select Build when a change is pushed to GitHub, when any changes made to our git repository    and commit,jenkins helps to trigger and build job with updated code.
           
           C. Now in Build -> Add build step -> Execute Shell
                     docker build -t <image-name>
           
           D. In Post-build actions -> Build other projects -> Projects to be build -> Specify jobs
                Specified jobs - docker-compose , FINAL JOB
          
           E. This way all the jobs are configured using above steps and this jobs gets executed when there are any changes made to dockerfile.
   
   

#Limitations :

 -> Before executing FINAL JOB job make sure all our containers are removed and killed orelse will get  error when job is triggered.
                docker kill <image-name>
                docker rm -f <image-name>
                docker run -i -d -p 8100:8100 --entrypoint=/entrypoint.sh <image-tag> -s   
                        

#Note :
          When Jenkins has to be triggered following are the steps to be followed 
          
        1. Open Github https://github.com/SusrithaMunukutla/yaml_file
        2. Go to Settings : https://github.com/SusrithaMunukutla/yaml_file/settings
        3. Go to Webhooks&Services : https://github.com/SusrithaMunukutla/yaml_file/settings/hooks
        4. In Services ->Add Service ->Jenkins(GitHub plugin) ->Add Jenkins  hook url :
                            http://<ip-address>:8080/github-webhook/
        
         
              
