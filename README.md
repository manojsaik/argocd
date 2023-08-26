# argocd Set up ArgoCD in Kuberenetes and deploy application using ArgoCD using Helm chart 
Prerequisites: 
- A working Kubernets cluster (Minikube or Docker-desktop k8s atleast)
- An SSH key pair on your local Linux/MacOS/BSD machine

**Step 1: **
Run ArgoCD as deployment in your K8s cluster 

 1. create a argocd namespace -
     $ kubectl create namespace argocd
 2. create a argocd deployment using apply cmd
     $ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
 3. Forward the argocd server port to your local machine port using cmd 
     $ kubectl port-forward svc/argocd-server -n argocd 8080:443 (this will run argocd in your localhost:8080/)
   Note: Port forwarding will block the terminal it’s running in as long as it’s active, so you’ll probably want to run this in a new terminal window while you continue to work
 4. Install argocd CLI to interact with argocd and settings  
     $ ​​/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"  
      or for homebrew - $ brew install argocd
 5. Get the creds to login to argocd by getting secret 
     $ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
 6. login to Argocd in web UI at localhost:8080 with admin username and password from above secret / through cli 
    $ argocd login localhost:8080

**Step 2:** 
Deploy a Application into K8s cluster using ArgoCD 

1. Add the target cluster where Argocd need to deploy if you have multiple cluster
   check the cluster info using  $ kubectl config get-contexts -o name
   set the cluster using : argocd cluster add target-k8s
2. Create APP using argocd cmd ( pass the github repo where you've helmchart defined , destination server & Namespace ) 
   $ argocd app create helm-nginx --repo https://github.com/manojsaik/argocd.git --path nginx-chart --dest-server https://kubernetes.default.svc --dest-namespace default
3. Get the app details using cmd
   $ argocd app get helm-nginx
   ( Not the app is in Outof sync state this is the default behaviour of app when created)
4. Deploy the app , in argocd terms sync the app will deploy the resources with in K8s
   $ argocd app sync helm-nginx
5. Confirm the Nginx is running on port mentioned in https://github.com/manojsaik/argocd/blob/main/nginx-chart/templates/service.yaml#L13 (30081) as of now

Thank you 
Manoj 

