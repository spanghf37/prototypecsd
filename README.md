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
    ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAih71xgU1jVr0W18W6ve09FMOXjSrfUH55/Fw7be+6bQGdGb7HERAHx66fymfjmgN5XxTwbD6SpU0+eFwOQNTKfgmNk9c+mosgBySKYFhRs+EBtLie2iySYAoRqxGTlTwsmmtsUULHtax67BIjEb+DwwySJiv/kFf1x3/O3heAXiBjj48meEreCroxC9HQs8X6msx7hntYpJgYkebxpZ+R0x61KnZhYaZ228v+FJuTlCUyT5YCV4xoCjlgf+vlYdKxyl86cZtwaySl9WkGDZxGInc6HhW3cLL3xddmnEM2YSV46TFsmoSss8De7HUMY9oltWmMNqt9joETNhT0xDLOw== rancher
    
    
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




    
    
    
    
    
