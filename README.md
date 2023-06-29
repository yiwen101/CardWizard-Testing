# Prerequisites
Run Nacos. 

I tried to inlclude a binary version of the nacos server in this repository, but the Github seems to have a 100 MB limit on the maximum file size per file and the nacos server's binary file unfortunately exceeds that limit. So, I have to trouble you guys to install the nacos server yourself.

The easiest way to run the nacos server is to run it in a docker container using the image `nacos/nacos-server`. More specificly, install docker/docker desktop [here](https://www.docker.com/products/docker-desktop/), then run the following command in the terminal:
 (this should pull the latest version)
-     docker pull nacos/nacos-server
(This should run the image. Please check the version of the image you pulled in case newer version than 2.0.3 is available)
-     docker run --name nacos-standalone -e MODE=standalone -p 8848:8848 -d nacos/nacos-server:2.0.3

You can access the Nacos web interface by opening a web browser and navigating to http://localhost:8848/nacos

Use `centralx/nacos-server` instead of `nacos/nacos-server` if your chip is ARM. 

Of course, you can also choose to install nacos via its [Github release page](https://github.com/alibaba/nacos/releases)

# How to use the Gateway
1. You could choose to test with our implemented testRPC server, or implement your own kitex based RPC server. 
If you wish to test our testRPC server, plase do not change the arithmetic.thrift file or change its place. Make sure that the naces server is running before running `./testRPCServer ` in the terminal. You should see this:
![Image 0](images/image%200.png)

Then run the gateway app by `./API_Gateway` in the terminal, and you can see the gateway running.
![Image 1](images/image%201.png)

2. If you choose to test with your own server, remember to put the thrift file in the IDL folder, or use the -useFolder flag to indicate the path to the folder containing all the thrift files.For example, ./API_Gateway -useFolder path/to/the/folder. 
![Image 2](images/image%202.png)
Please choose to support json-thrift generic call and nacos service registry in your server. More details can be found in the Kitex [documentation](https://www.cloudwego.io/docs/kitex/tutorials/advanced-feature/generic-call/).


Also, if you wish to assign custom routes via annotation, make sure your thrift file's annotation does not contradict with the [IDL Definition Specification for Mapping between Thrift and HTTP](https://www.cloudwego.io/docs/kitex/tutorials/advanced-feature/generic-call/thrift_idl_annotation_standards/).



## Expected Behaviors
### Default Route
For all services and methods, it will be automatically registered with route `/:serviceName/:methodName` under Post method. 
For example, the `CreateNote` method of the NoteService will be registered under `Post localhost:8080/NoteService/CreateNote`.

![Image 3](images/image%203.png)


### Customized Route
You could also assign customized routes by annotating the idl file like such:

![Image 4](images/image%204.png)

Then the Add method will also be registered at `Get localhost:8080/arith/add`.

## Demonstration on Route
- A successful call to the `Add` method of the `arithmetic` service at path `/arithmetic/Add` under the POST method.
  
![Image 5](images/image%205.png)

- The call to `/arith/add` under GET method is also successful, as it is a customized route as annotated in the IDL file.
  
![Image 7](images/image%207.png)

- A case with no matching route.
 
 ![Image 6](images/image%206.png)


## Parameter Validation and RPC Call
Parameters to the method as stipulated in the IDL files should be passed as a JSON-encoded body in your HTTP request.

The gateway will automatically validate it before making the RPC call. Error messages will be displayed to the user to indicate any issues that lead to failure of validation. If validation is successful, it will forward the RPC response received from upstreams back.

## Demonstration on Validation and RPC Call
- Error message highlighting type mismatch for argument passed in.

![Image 8](images/image%208.png)

- Error message highlighting field mismatch for argument passed in.

![Image 9](images/image%209.png)

- Redundant fields do not hamper validation; can still get correct response.

![Image 10](images/image%2010.png)



