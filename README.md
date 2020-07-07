# prototypecsd

1) VMs avec image RancherOS. Corriger MTU à 1460 (dans cloud-config)

    Dans GCE, créer les VM avec metadonnées :   
    key=user-data   
    value=
    #cloud-config
    rancher:
      docker:
        extra_args: [--mtu, 1460]
        
    Dans Sécurité de la VM / SSH copier clé SSH pour l'utilisateur  "rancher" :
    ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAr7uY8Y5PiGwXva8RxSqOkjyDtY3BRXmt3WM2K7cDd2bmJ6+1I2pKh8j5k1ilRThbnCqC9vI9mvv9F1EdU3kQOPIqnrz/jdPlHy8Efy8wQB+Y4u2lNT5bmkeC2A1ZdraKkQ5uyIuyFWPHHlE/cbEnRIE7SHuXeQLwojTx7df3p1LYppbV8LHolmUYQ5bxiv6FmbBFDJ1JHPRQvSU8ht4UuUnL8nMuDnLl1LqxU3YEKlRMMXl2F8QhIoCh4dcd4MvVGgFSRPbYmnYybbaf/HXdcB2Tb71awpf8NkzNzTzyQ4uymfUb3f0Nr7HBRgN5k+7sh79g62y6j9XKP5eW3D77EQ== rancher
    
    
    Dans change VM RancherOS: sudo ros config set rancher.network.interfaces.eth1.mtu 1460
    
    Dans chaque VM RancherOS, générer ssh-keygen et ajouter les 3 clés publiques dans le fichier .ssh/authorized_keys (avec VI)
    
2) Installer  kubectl   et rke dans la VM RancherOS-Rancher
wget https://storage.googleapis.com/kubernetes-release/release/v1.18.5/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/bin/kubectl
kubectl version --client


wget https://github.com/rancher/rke/releases/download/v1.1.3/rke_linux-amd64
mv rke_linux-amd64 rke
chmod +x rke
sudo mv rke /usr/bin/
rke --version

3) Sur chaque VM RancherOS, lancer ssh-keyghen. Puis remplacer les fichiers générés id_rsa et id_rsa.pub par ceux générés par PuttyGen

4) Installation de RANCHER sur un cluster type single :
Préparer fichier rancher-cluster.yml et lancer rke up --config ./rancher-cluster-single.yml
Ce va déployer un cluster Kubernetes.

Ensuite, installer Helm 3. Puis déployer le catalog Rancher sur le cluster Kubernetes.
Tester Helm avec "helm help"
Puis : helm repo add rancher-latest https://releases.rancher.com/server-charts/latest

kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.15.0/cert-manager.crds.yaml

kubectl create namespace cert-manager
helm repo add jetstack https://charts.jetstack.io
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v0.15.0

kubectl get pods --namespace cert-manager

helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancheros-rancher-1.duckdns.org

kubectl -n cattle-system rollout status deploy/rancher


6) Installation du Master Kubernetes et des Workers Kubernetes
Sur d'autres VM avec socle RancherOS, lancer les commandes Docker générées lors de l'ajout d'un nouveau cluster dans l'interface Rancher. Attention à préciser les IP internes et externes.




    
    
    
    
    
