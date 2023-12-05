

```bash
# kubeconfig 파일로부터 CA 인증서를 추출하는 방법
kubectl config view --minify --raw --output 'jsonpath={..cluster.certificate-authority-data}' | base64 -d > k8s-ca.cert

# --cacert 옵션으로 사용자가 명시적으로 CA 인증서를 제공
curl --cacert k8s-ca.cert https://10.0.0.1:6443
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
  "reason": "Forbidden",
  "details": {},
  "code": 403
}%
```





출처
https://coffeewhale.com/apiserver