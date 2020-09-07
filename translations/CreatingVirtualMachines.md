# LABS : Creating Virtual Machines

## Objectives 

In this lab, you explore the available options for VMs and see the differences between locations.

In this lab, you learn how to perform the following tasks:

    -Create several standard VMs

    -Create advanced VMs

## Steps 

1. Create a utility virtual machine

    -Use the command below to create a utility virtual machine 

        gcloud beta compute --project=qwiklabs-gcp-04-546ddcc886ba instances create instance-1 --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address --maintenance-policy=MIGRATE --service-account=729795876533-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=instance-1 --reservation-affinity=any

2. Create a Windows virtual machine

    -Use the command below to create a windows virtual machine 

        gcloud beta compute --project=qwiklabs-gcp-04-546ddcc886ba instances create instance-windows --zone=europe-west2-a --machine-type=n1-standard-2 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=729795876533-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=instance-windows --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any

        gcloud compute --project=qwiklabs-gcp-04-546ddcc886ba firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

        gcloud compute --project=qwiklabs-gcp-04-546ddcc886ba firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server

3. Create a custom virtual machine

    -Use the command below to create a custom virtual machine

        gcloud beta compute --project=qwiklabs-gcp-04-546ddcc886ba instances create custvm --zone=us-west1-b --machine-type=custom-6-32768 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=729795876533-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=custvm --reservation-affinity=any

    -For the custom VM you just created, click SSH.

    -To see information about unused and used memory and swap space on your custom VM, run the following command:

        free

    -To see details about the RAM installed on your VM, run the following command:

        sudo dmidecode -t 17

    -To verify the number of processors, run the following command:

        nproc

    -To see details about the CPUs installed on your VM, run the following command:

        lscpu
    -To exit the SSH terminal, run the following command:

        exit

