This location contain files related to the dokumentation page - https://docs.evolveum.com/midpoint/install/kubernetes/ .

ca.pem ... File containing CA cert used for signing of the certificate

-------------
option 1
 
Pod with generic repository in local H2 file.


kubectl apply -f namespace-mp-demo.yaml -f pod-h2.yaml

kubectl get -n mp-demo namespace/mp-demo pod/mp-h2-demo

kubectl delete -n mp-demo pod/mp-h2-demo

-------------
option 2 

... like option 1 but the pod is created using statefulset object


kubectl apply -f namespace-mp-demo.yaml -f statefulset-h2.yaml

kubectl get -n mp-demo namespace/mp-demo statefulset.apps/mp-h2-demo

kubectl delete -n mp-demo statefulset.apps/mp-h2-demo

-------------
option 3 - generic repository

Dedicated pod with postgresql DB - without native repository DB init (generic repository).
Midpoint pointing to the postresql DB using defined service.


kubectl apply -f namespace-mp-demo.yaml -f statefulset-db-w_o-init.yaml -f service-db.yaml -f statefulset-pg-generic.yaml

kubectl get -n mp-demo namespace/mp-demo statefulset.apps/mp-demo-db statefulset.apps/mp-pg-demo service/mp-demo-db

kubectl delete -n mp-demo statefulset.apps/mp-demo-db service/mp-demo-db statefulset.apps/mp-pg-demo

-------------
option 4 - native repository

Dedicated pod with postgresql DB - with native repository DB structure init.
Midpoint pointing to the postresql DB using defined service.


kubectl apply -f namespace-mp-demo.yaml -f statefulset-db-pg-native.yaml -f service-db.yaml -f statefulset-pg-native.yaml

kubectl get -n mp-demo namespace/mp-demo statefulset.apps/mp-demo-db service/mp-demo-db statefulset.apps/mp-pg-demo

kubectl delete -n mp-demo statefulset.apps/mp-demo-db service/mp-demo-db statefulset.apps/mp-pg-demo

-------------
To be able to reach the environment for option 3 or option 4 there are needed few more objects (common for both options)


kubectl apply -f service-midpoint.yaml -f secret-cert-mp-pg-demo.yaml -f ingress-midpoint.yaml

kubectl get -n mp-demo service/mp-pg-demo secret/cert-mp-pg-demo ingress.networking.k8s.io/mp-pg-demo

kubectl delete -n mp-demo service/mp-pg-demo secret/cert-mp-pg-demo ingress.networking.k8s.io/mp-pg-demo


The result is defined FQDN on the ingress for the https://mp-pg-demo.mp-demo.local/ with provided certificate.
To be able to connect you need to confirm trust for the certificate or import provided ca.pem cert (root ca cert) as trusted cert.
It have to be possible to resolve mp-pg-demo.mp-demo.local to IP address of the kubernetes node - e.g. manual record to /etc/hosts file.