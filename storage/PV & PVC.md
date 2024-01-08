# How to create PV and PVC in Kubernetes
# Persistent Volume (PV)
Kubernetes makes physical storage devices such as our SSDs, NFS servers available to your cluster in the form of objects called Persistent Volumes.

It is a piece of pre-provision storage inside the cluster and provision by Administrator.

Data Inside this volumes can exist beyond the Life-cycle of pod.

A Persistent Volume is an abstraction for the physical storage device that you have attached to the cluster and pods can use this storage space using Persistent Volume Claims.

# Persistent Volume Claim (PVC)
Persistent Volume Claim is a storage request by a user, typically developer.

Developer request for some capacity along with some access mode like read/write or read-only.

Claims can request specific size and access modes, for example they can be mounted once read/write or many times read-only.

If none of the static persistent volumes match the user’s PVC request, the cluster may attempt to dynamically create a PV that matches the PVC request based on storage class.

# Creating PV and PVC :-
Example: Claiming 3GB storage from the 10GB capacity.

# PV Manifest file (pv-1.yml)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume-2
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"

# PVC Manifest file (pvc-claim.yml)
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-claim-2
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi

# Commands to run:
Create the persistence volume file using :
$ kubectl create -f pv.yml

2. Get the persistence volume information :
$ kubectl get pv  pv-volume-2

# 3. Create the persistence volume claim file using:
$ kubectl create -f pvc-1.yml

# 4. Check for the status of PV :
$ kubectl get pv  pv-volume-1

# 5. Claiming the required storage :
$ kubectl get pvc pvc-claim-1

# Clean Up : –
$ kubectl delete pv -f pv-1.yml
$ kubectl delete pvc -f pvc-claim.yml

# References:-

https://kubernetes.io/docs/concepts/storage/persistent-volumes/