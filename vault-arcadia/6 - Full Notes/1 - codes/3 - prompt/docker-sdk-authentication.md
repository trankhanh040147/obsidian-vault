```

vessel@vessel-pc:~$ curl https://dev-docker-socket-proxy.signetcenter.dev/version
401 Unauthorized
vessel@vessel-pc:~$ curl -u admin:ME4yy5K7nLhneQLQnUbh9YqmNAcNghKg https://dev-docker-socket-proxy.signetcenter.dev/version
{"Platform":{"Name":"Docker Engine - Community"},"Components":[{"Name":"Engine","Version":"20.10.17","Details":{"ApiVersion":"1.41","Arch":"amd64","BuildTime":"2022-06-06T23:01:03.000000000+00:00","Experimental":"false","GitCommit":"a89b842","GoVersion":"go1.17.11","KernelVersion":"5.4.0-216-generic","MinAPIVersion":"1.12","Os":"linux"}},{"Name":"containerd","Version":"1.6.6","Details":{"GitCommit":"10c12954828e7c7c9b6e0ea9b0c02b01407d3ae1"}},{"Name":"runc","Version":"1.1.2","Details":{"GitCommit":"v1.1.2-0-ga916309"}},{"Name":"docker-init","Version":"0.19.0","Details":{"GitCommit":"de40ad0"}}],"Version":"20.10.17","ApiVersion":"1.41","MinAPIVersion":"1.12","GitCommit":"a89b842","GoVersion":"go1.17.11","Os":"linux","Arch":"amd64","KernelVersion":"5.4.0-216-generic","BuildTime":"2022-06-06T23:01:03.000000000+00:00"}
vessel@vessel-pc:~$

```
How to login docker using commands ?

```
docker login -u admin -p ME4yy5K7nLhneQLQnUbh9YqmNAcNghKg https://dev-docker-socket-proxy.signetcenter.dev
```