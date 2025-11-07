# Build (with proxy)
```bash
sudo docker build --build-arg http_proxy=http://10.1.102.3:3128 --build-arg https_proxy=http://10.1.102.3:3128 -t sigpro-support-event .
```

```bash
sudo docker build --build-arg http_proxy=http://10.1.102.3:3128 --build-arg https_proxy=http://10.1.102.3:3128 -t sigpro-mapp-api .
```
# Remove and Build (image `app`)
```
docker rmi app && docker build --build-arg http_proxy=http://10.1.102.3:3128 --build-arg https_proxy=http://10.1.102.3:3128 -t app .
```
# Trivy
```
trivy image app
```
---
#cve
- [ ] carlos env11
- [ ] jwt v5
- [ ] autoIndex
- [ ] CMD Dockerfile
- [ ] go version
---