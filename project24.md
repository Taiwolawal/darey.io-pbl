
kubectl issue resolved by reverting from 1.24.0 to 1.23.6

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.23.6/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```


Link to get krew installation https://krew.sigs.k8s.io/docs/user-guide/setup/install/
```
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)
```

![image](https://user-images.githubusercontent.com/50557587/180186584-ad5f2327-3a32-443f-8fd6-3c8ac9236392.png)

```
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
kubectl krew install konfig
kubectl konfig import --save kubeconfig
```

![image](https://user-images.githubusercontent.com/50557587/180189234-2c128873-0ff9-4d40-86ff-63383029f667.png)

![image](https://user-images.githubusercontent.com/50557587/180189399-ad9dca37-9e71-4083-acf6-79f05a5f5ebe.png)

![image](https://user-images.githubusercontent.com/50557587/180194192-fc4d2fc9-76b3-4872-9718-ecd72f868ea8.png)


![image](https://user-images.githubusercontent.com/50557587/180192232-d7e52a67-1c9a-4040-827c-32e4d15a5358.png)

![image](https://user-images.githubusercontent.com/50557587/180192798-39b06312-a7ae-4145-b808-4a745ad819f2.png)

![image](https://user-images.githubusercontent.com/50557587/180193000-6f95bcda-bc18-499a-a3e1-829f06c22c46.png)

![image](https://user-images.githubusercontent.com/50557587/180194007-6fd3178c-5080-4f7c-8a0c-18f582d7c9af.png)



