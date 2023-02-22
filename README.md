# ASP.NET Core In Fly.io

Publishing a ASP.NET Core App in Fly.io.

Note: I discovered this while working (work in progress) on a more complex declarative web app framework using html/json which i am using fly.io to deploy <a href="https://aspnet1.fly.dev">guiapp<a> app for publishing static data webapps in Git Repositories

Note : Docker is not required and hence can be done in Windows under Parallels in macos for those who don't have nested virtualization enabled/not available.

# Step 1 - Create a Demo Asp.Net Core Project

Create a Demo Asp.Net Core Mvc Project in an Empty Directory

```
    dotnet new mvc -o AspNetDemo
    code -r AspNetDemo
```

# Step 2 - Modify the Asp.Net Core Project

Modify the Asp.NET core Project to add the following property

```
    <ServerGarbageCollection>false</ServerGarbageCollection>;
```

The above setting will reduce the memory used by Asp.NET core so that the app can be deployed in the free tier of fly.io service

# Step 3 - Publish the Asp.Net Core Project

Move to the Asp.Net Core Project Directory and run the following publish command to generate a 

```
    dotnet publish -r linux-musl-x64 --output "..\linux64_musl" --self-contained true -p:PublishSingleFile=true -p:PublishTrimmed=true
```

# Step 4 - Create a Docker File as below

```
    FROM mcr.microsoft.com/dotnet/runtime-deps:6.0-alpine-amd64

    EXPOSE 8080

    RUN mkdir /app
    WORKDIR /app
    COPY ./linux64_musl/. ./

    RUN chmod +x ./AspNetDemo
    CMD ["./AspNetDemo", "--urls", "http://0.0.0.0:8080"]
```

# Step 5 - Ensure that you have installed fly.io

Ensure that you have installed fly.io client and done the getting started guide in <a href="https://fly.io/docs/hands-on/start/">Fly.IO Getting Started</a>

Step 6 - Deploy AspNetDemo app to fly.io

Deploy the AspNetDemo app to fly.io using below commands. Ensure that the commands are run in the directory where the docker image exists

```
    flyctl launch
```

Enter the AppName : aspnet1
    
Select the Organization : Personal
    
Select the Region : Singapore

Step 7 - Verify the deployment

Verify the deployment by opening the browser and entering the following url in the following format

```
    https://aspnet1.fly.dev
```


