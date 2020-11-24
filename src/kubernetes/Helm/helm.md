# Helm

<pre><code>
# install helm
curl -LO https://storage.googleapis.com/kubernetes-helm/helm-v2.8.2-linux-amd64.tar.gz
tar -xvf helm-v3.0.0-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/

# Add repository 
helm repo list
helm repo add stable https://charts.helm.sh/stable

# Initialize helm
helm init
helm repo update

# Search helm chart
helm search spark
helm inspect stable/spark

# deploy the chart to the cluster
helm install --name my-first-spark-helm stable/spark

# view all packages
helm ls

# Create your own chart
helm create goform
helm lint
helm package goform
helm install goform ./goform-0.1.0.tgz

</pre></code>