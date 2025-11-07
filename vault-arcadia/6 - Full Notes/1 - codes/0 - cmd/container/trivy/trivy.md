export http_proxy='http://10.1.102.3:3128'
export https_proxy='http://10.1.102.3:3128'
trivy image --timeout 10m --scanners vuln sigpro-cronjob
