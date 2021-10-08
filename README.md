# cnand_udaconnect

1. Setup The Environment

Install the tools to get our environment set up properly.

If you haven't already done so:

   1. Install Docker
   2. Set up a DockerHub account
   3. Set up kubectl
   4.  Install VirtualBox with at least version 6.0
   5.  Install Vagrant with at least version 2.0

2. Initialize K3s

To run the application, you will need a K8s cluster running locally and interface with it via kubectl. We will be using Vagrant with VirtualBox to run K3s.

In this project's root, run vagrant up.

```
vagrant up
```

The command will take a while and will leverage VirtualBox to load an openSUSE OS and automatically install K3s. Some useful vagrant commands can be found in this cheatsheet. A good introduction to Vagrant can be found in this article.
3. Retrieve the Kubernetes config File

After vagrant up is done, you will SSH into the Vagrant environment and retrieve the Kubernetes config file used by kubectl. We want to copy this file's contents into our local environment so that kubectl knows how to communicate with the K3s cluster.

We want to copy this file's contents into our local environment so that kubectl knows how to communicate with the K3s cluster.
```
sudo cat /etc/rancher/k3s/k3s.yaml
```
The output should look like this:
```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJWekNCL3FBREFnRUNBZ0VBTUFvR0NDcUdTTTQ5QkFNQ01DTXhJVEFmQmdOVkJBTU1HR3N6Y3kxelpYSjIKWlhJdFkyRkFNVFU1T1RrNE9EYzFNekFlRncweU1EQTVNVE13T1RFNU1UTmFGdzB6TURBNU1URXdPVEU1TVROYQpNQ014SVRBZkJnTlZCQU1NR0dzemN5MXpaWEoyWlhJdFkyRkFNVFU1T1RrNE9EYzFNekJaTUJNR0J5cUdTTTQ5CkFnRUdDQ3FHU000OUF3RUhBMElBQk9rc2IvV1FEVVVXczJacUlJWlF4alN2MHFseE9rZXdvRWdBMGtSN2gzZHEKUzFhRjN3L3pnZ0FNNEZNOU1jbFBSMW1sNXZINUVsZUFOV0VTQWRZUnhJeWpJekFoTUE0R0ExVWREd0VCL3dRRQpBd0lDcERBUEJnTlZIUk1CQWY4RUJUQURBUUgvTUFvR0NDcUdTTTQ5QkFNQ0EwZ0FNRVVDSVFERjczbWZ4YXBwCmZNS2RnMTF1dCswd3BXcWQvMk5pWE9HL0RvZUo0SnpOYlFJZ1JPcnlvRXMrMnFKUkZ5WC8xQmIydnoyZXpwOHkKZ1dKMkxNYUxrMGJzNXcwPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://127.0.0.1:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    password: 485084ed2cc05d84494d5893160836c9
    username: admin
```
Copy the content and type exit to exit the virtual OS. You will find yourself back in your computer's session.

Retrieve the Kubernetes config file", add "A useful article on how to manage kubeconfig files can be found here.

4. Configure kubectl

Create the file ~/.kube/config (or replace it if it already exists).

Paste the contents of the k3s.yaml output into the config file.

Test that kubectl works by running the command

kubectl describe services

It should not return any errors.

5. Deploy kubectl


    i. ```kubectl apply -f deployment/db-configmap.yaml``` - Set up environment variables for the pods
    
    ii. ```kubectl apply -f deployment/db-secret.yaml``` - Set up secrets for the pods
    
    iii. ```kubectl apply -f deployment/postgres.yaml``` - Set up a Postgres database running PostGIS
    
    iv. ```kubectl apply -f deployment/udaconnect-api.yaml``` - Set up the service and deployment for the API
    
    v. ```kubectl apply -f deployment/udaconnect-app.yaml``` - Set up the service and deployment for the web app
    
    vi. ```sh scripts/run_db_command.sh <POD_NAME>``` - Seed your database against the postgres pod. (kubectl get pods will give you the POD_NAME)

Manually applying each of the individual yaml files is cumbersome, but going through each step provides some context on the starter project's content. In practice, we can reduce the number of steps by running the command against a directory to apply all of the contents:
```
kubectl apply -f deployment/
```
6. Seed the Database

The first time you run this project, you will need to seed the database with dummy data. Use the command:
```
sh scripts/run_db_command.sh <POD_NAME>
```
against the postgres pod. (kubectl get pods will give you the POD_NAME). Subsequent runs of kubectl apply to make changes to deployments or services will not require you to seed the database again!
7. Verify Your Deployment

Once the project is up and running, you should be able to see three deployments and three services in Kubernetes. To verify, run the following commands:
```
kubectl get pods
kubectl get services
```
Both should both return udaconnect-app, udaconnect-api, and postgres.

These pages should also load on your web browser:

    http://localhost:30001/ - OpenAPI Documentation
    http://localhost:30001/api/ - Base path for API
    http://localhost:30000/ - Frontend ReactJS Application

