# Build Your First Azure SignalR Service Application (cloud)

```bash
# envs
loc=westus
rg=chatrg
signalr=bjwchatsignalrclassic # imagine this has to be globally unique per dns
connectionString=TODO # will be setup after signalR is running
```

```bash
# create resource group
az group create --location $loc --resource-group $rg
```

```bash
# create signalr resource
# free: 20 connections, 20k messages
# standard: 1000 connections, 1M messages (per unit, up 100 units)
# cannot send http reqs directly to serverless signalR; must use fx w/ http trigger
az signalr create --name $signalr \
  -g $rg \
  -l $loc \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Classic # serverless WON'T WORK
```

```bash
# set connection string env
connectionString=$(az signalr key list -n $signalr -g $rg --query primaryConnectionString -o tsv)
echo $connectionString
```

```bash
# configure/run webapp (C# local project)
dotnet restore
dotnet user-secrets set Azure:SignalR:ConnectionString $connectionString
dotnet run
```

When you open http://localhost:5000, you can see the application runs as usual, just instead of hosting a SignalR runtime by itself, it connects to the SignalR service running on Azure.

In this sample, you have learned how to use Azure SignalR Service to replace your self-hosted SignalR runtime. But you still need a web server to host your hub logic. In the next tutorial you'll learn how to use other Azure services to host your hub logic so you can get everything running in the cloud.
