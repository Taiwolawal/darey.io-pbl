![image](https://user-images.githubusercontent.com/50557587/162760785-f1cc6fef-7a81-480d-a50c-9124e7bec640.png)

kubectl installation on windows https://www.youtube.com/watch?v=UE1UqcaSYpM&ab_channel=yasinlachini
![image](https://user-images.githubusercontent.com/50557587/162772044-4ea55774-a7dc-4d32-84e0-5332aa6d15e5.png)
![image](https://user-images.githubusercontent.com/50557587/162779719-3cdd8f13-1ff9-4016-a0ed-7053bcd32fb0.png)
![image](https://user-images.githubusercontent.com/50557587/162780073-79ff491b-7c30-4981-986f-2968522c032a.png)
![image](https://user-images.githubusercontent.com/50557587/162781280-ec1f6cb7-6f3a-417c-b4af-d422947cdfa5.png)
![image](https://user-images.githubusercontent.com/50557587/162781512-d83d0333-8429-4a46-aa6c-96ee384e4b89.png)
![image](https://user-images.githubusercontent.com/50557587/162781884-3fd33496-57bd-4c92-bdfd-1b6320a473e4.png)
![image](https://user-images.githubusercontent.com/50557587/162782263-e6882fe7-9776-44f3-b6ed-a8e49040be13.png)
![image](https://user-images.githubusercontent.com/50557587/162782515-698a8141-b467-4a1a-927c-371ff32c7c9a.png)
![image](https://user-images.githubusercontent.com/50557587/162782551-1dc3ddbf-0501-49e8-b0cb-cdb4eac05e58.png)
![image](https://user-images.githubusercontent.com/50557587/162782985-25e6662c-a264-4015-a36a-14874b4b721c.png)
![image](https://user-images.githubusercontent.com/50557587/162783334-4c2c31a5-2862-4a18-a160-d7c68bf49fda.png)
![image](https://user-images.githubusercontent.com/50557587/162784238-d80f05c9-e672-464d-8e59-1faed20fab09.png)
![image](https://user-images.githubusercontent.com/50557587/162784363-6cfa4a55-e7f2-44d7-98f5-95af98caac7c.png)
![image](https://user-images.githubusercontent.com/50557587/162784442-57a4e8a4-17ab-4006-9b0a-293c39b62ee2.png)
![image](https://user-images.githubusercontent.com/50557587/162784676-ae98744d-3683-4f9e-aad2-a968206d48e2.png)
![image](https://user-images.githubusercontent.com/50557587/162785747-10db4d5e-164d-44d7-bb69-d8efdfb3c395.png)
![image](https://user-images.githubusercontent.com/50557587/162785954-28d7761d-3f01-4951-9adc-8a623b5ff86c.png)
![image](https://user-images.githubusercontent.com/50557587/162786126-5b7f6ffb-f347-4249-af30-f2e782ed261b.png)
![image](https://user-images.githubusercontent.com/50557587/162786848-9f5e8e94-5f3d-423e-86ce-e3ab5b23e496.png)
![image](https://user-images.githubusercontent.com/50557587/162788067-1d03d47f-300b-4d43-b995-62b8503929fe.png)
![image](https://user-images.githubusercontent.com/50557587/162788196-0c66dabf-1af1-4c6b-bb31-1e95e1250072.png)
![image](https://user-images.githubusercontent.com/50557587/162788739-079719ee-922b-45ee-ae5c-c21541ee662a.png)
![image](https://user-images.githubusercontent.com/50557587/164046891-d8ab979a-4966-450c-99c3-45891060c7b2.png)

```
for i in 0 1 2; do
  instance="${NAME}-worker-${i}"
  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')
  scp -i ../ssh/${NAME}.id_rsa \
    kube-proxy.kubeconfig k8s-cluster-from-ground-up-worker-${i}.kubeconfig ubuntu@${external_ip}:~/; \
done
```

```
for i in 0 1 2; do
  instance="${NAME}-master-${i}"
  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')
  scp -i ../ssh/${NAME}.id_rsa \
    admin.kubeconfig kube-controller-manager.kubeconfig kube-scheduler.kubeconfig  ubuntu@${external_ip}:~/; \
done
```

![image](https://user-images.githubusercontent.com/50557587/164048787-3cd1eba5-54b4-48a9-abe4-254b1b78929e.png)
![image](https://user-images.githubusercontent.com/50557587/164084830-96d23f97-dfd2-4dcb-9d41-e4acb6521f6b.png)
![image](https://user-images.githubusercontent.com/50557587/165284707-a1cf3faf-c97a-441a-b685-b7039afe1f7d.png)
![image](https://user-images.githubusercontent.com/50557587/165284780-cc38d2d5-509a-49a8-8fdf-bce1a9a9b4f0.png)
![image](https://user-images.githubusercontent.com/50557587/165288094-5a8a9a43-c9a2-40f6-aa63-893ae66f3f76.png)
![image](https://user-images.githubusercontent.com/50557587/165453305-a0378ab2-3f57-4235-9991-06ba3572d88b.png)
![image](https://user-images.githubusercontent.com/50557587/165453445-2402cd53-ff81-401d-a8b2-982a8ea0184d.png)
![image](https://user-images.githubusercontent.com/50557587/165453713-b90ff722-48e2-40a2-bd86-1e702e49b403.png)
![image](https://user-images.githubusercontent.com/50557587/165453981-627f752e-5f10-49b6-a120-e29c9334748b.png)
![image](https://user-images.githubusercontent.com/50557587/165454116-e20cf61f-cb08-497c-ba45-2cd0751c1ce6.png)
![image](https://user-images.githubusercontent.com/50557587/165454220-48a1dffe-1fec-4684-9bc0-66a648f108cc.png)
![image](https://user-images.githubusercontent.com/50557587/165454423-ed2388c0-4b3f-45cb-a1a0-b92c4c4af18e.png)
![image](https://user-images.githubusercontent.com/50557587/165466866-cb9db8ce-5fe4-41d8-bb28-9fc9dfd4f578.png)
![image](https://user-images.githubusercontent.com/50557587/165467116-3122ae45-61f4-48da-833e-a137873e4f2b.png)
![image](https://user-images.githubusercontent.com/50557587/165469775-f6d4917b-1e83-45c9-9216-eae1b7cafc8d.png)
![image](https://user-images.githubusercontent.com/50557587/165470727-155c54cb-77c4-42bc-b656-dd69cefd9268.png)

