# cpp-ci
This project is about the compile integration for c/c++.

## Deploy

### Install

#### 1. Fetch the sdk and enter the path
```
git clone https://github.com/akley-MK4/sdk.git
cd cpp-ci
```

#### 2. Build image
Build an image locally for docker or containerd to use.
##### 2.1. Docker
```
make docker-build
```
##### 2.2. Containerd
```
make dc-build
```

#### 3. Install the chart
```
cd ./charts/cpp-ci
helm install cpp-ci ./
```

## How to use it
### 1. As a server
#### 1.1. Login the server using ssh
```
# from default setting
ssh op1@node1 -P 32011
```

#### 1.2. Fetch the code and build it
```
cd ~/${mountPath}
git clone xx
# execute your build command
./build.sh
```

### 2. For CI-CD