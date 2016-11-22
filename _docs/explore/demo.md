---
layout: guide
title: Demonstration
anchor: explore
module: explore/demo
---

# Persistent Storage Demonstration

In this exercise we will demonstrate how StorageOS can provide persistent storage to Docker containers.  The YouTube video below plays through the steps we will run through in this section that follows below.

<iframe width="640" height="480"
   src="https://www.youtube.com/embed/jrWRoILSyjQ?version=3&vq=hd720" frameborder="0" allowfullscreen>
</iframe>

You will need access to the internet to pull down a container for this demonstration, a web browser and a terminal connection to one of your StorageOS nodes to complete this exercise.

## Connect to a StorageOS node

To get started you will need to be connected to a StorageOS node and load a container for this demonstration using the `docker run` command.

```bash
vagrant@storageos-3:~$ docker run -it -d -v crasher-data:/data --volume-driver=storageos -p 8081:8081 karolisr/crasher
Unable to find image 'karolisr/crasher:latest' locally
latest: Pulling from karolisr/crasher
8ddc19f16526: Pull complete
0738bcb6df0b: Pull complete
Digest: sha256:4313467201fb28e55cfefcd0c03d11892470335342c91ee94ce05b3ef4dbca66
Status: Downloaded newer image for karolisr/crasher:latest
2e26c041aff876577a4f5535aeedcb14ce838fbb2b72b7706d2740d41a93526b
```

Confirm the container installed and is running.

```bash
vagrant@storageos-3:~$ docker ps -a | grep crasher
2e26c041aff8  karolisr/crasher  "/bin/crasher"  About a minute ago  Up About a minute  0.0.0.0:8081->8081/tcp  stoic_franklin
```

>**&#x270F; Note**: We are using port 8081 for this container, so for our Vagrant based installation on node 3 we will use `10.245.103.4:8081` to connect to our demonstration application container.
>
>**&#x270F; Note**: For the ISO based installation, use the IP address you configured and append this with the port number `8081`.

## Open the Crasher container application

Lets open the Crasher container application in a new browser window using the IP and port number as noted above.

<img src="/images/docs/explore/crasher1.png" width="640">

Click on the <img src="/images/docs/explore/count1.png" height="22"> button to increment the counter and then click on <img src="/images/docs/explore/crash1.png" height="22"> to crash the application.  If you refresh the browser you will be unable to reconnect to the application.

Now go back to the terminal window and use the 'docker restart' command to restart the Crasher container - you will need to use the container ID that was outputted by the `docker ps -a` command you used previously.

```bash
vagrant@storageos-3:~$ docker restart 2e26c041aff8
2e26c041aff8
```

Now return back to the browser window and refresh the contents.  You should see that StorageOS was able to maintain the count just before you crashed the application (refreshing the window will cause it to increment the count by 1).

<img src="/images/docs/explore/crasher15.png" width="640">

## Remove provisioned volume

Now let us clean-up the container and clean-up the provisioned volume.

You can list out the 10GB crasher-data volume using the `storageos` CLI.

```bash
vagrant@storageos-3:~$ storageos cli volume list
ID                                    NAME          DESCRIPTION  SIZE  DC
c3711ff5-404e-f494-7b90-7314bd0995a2  crasher-data               10
```

To delete the volume you can use the `storageos` CLI.

```bash
vagrant@storageos-3:~$ storageos cli volume delete -id=c3711ff5-404e-f494-7b90-7314bd0995a2
==> Deleted volume: c3711ff5-404e-f494-7b90-7314bd0995a2
```

--or--

You can view the 10GB crasher-data volume from the Web UI by navigating to **Provisioning**, **Volumes**.

And to delete the volume, simply click on the &#x1F5D1; icon.

<img src="/images/docs/explore/volumes.png" width="640">

## Remove Crasher container

The Crasher application container can be removed using the `docker` command from your terminal window.

Get the container ID and stop the container.

```bash
vagrant@storageos-3:~$ docker ps -a | grep crasher
2e26c041aff8        karolisr/crasher                       "/bin/crasher"           2 hours ago         Up About an hour            0.0.0.0:8081->8081/tcp                                                                                          stoic_franklin
vagrant@storageos-3:~$ docker stop 2e26c041aff8
2e26c041aff8
```

Remove the container.

```bash
vagrant@storageos-3:~$ docker rm 2e26c041aff8
2e26c041aff8
```

Get the Docker image ID and delete the image and dependencies.

```bash
vagrant@storageos-3:~$ docker images | grep crasher
karolisr/crasher                 latest              6ec99f1d2819        7 weeks ago         8.442 MB
vagrant@storageos-3:~$ docker rmi 6ec99f1d2819
Untagged: karolisr/crasher:latest
Untagged: karolisr/crasher@sha256:4313467201fb28e55cfefcd0c03d11892470335342c91ee94ce05b3ef4dbca66
Deleted: sha256:6ec99f1d281932b8981b15a23d2f6b27264c8c3f41854db2e10531c8bb42f059
Deleted: sha256:673f3909c8494367736aac791b205d68ac7b190480396fe43864d42eab2f92b0
Deleted: sha256:8ac8bfaff55af948c796026ee867448c5b5b5d9dd3549f4006d9759b25d4a893
```