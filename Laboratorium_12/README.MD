# Instrukcja realizacji laboratorium

## 1. Założenie prywatnego repozytorium na DockerHub

![lab12 png1](img/png1.png)

## 2. Przygotowanie pliku Dockerfile

Dockerfile:
```dockerfile
FROM nginx:latest
```

## 3. Zbudowanie obrazu i przesłanie go do utworzonego wcześniej repozytorium z tagiem: *lab12*

```bash
docker build -t damianboguszpl66/private:lab12 .
docker login
docker push damianboguszpl66/private:lab12
```
![lab12 png3](img/png3.png)

![lab12 png2](img/png2.png)

## 4. Przygotowanie manifestu Pod-a *test12*

Specyfikacja Pod-a:
- utworzony w domyślnej przestrzeni nazw,
- o nazwie test12,
- w oparciu o odpowiednio skonfigurowany obiekt Secret, będzie wykorzystywał obraz znajdujący się na utworzonym wcześniej prywatnym repozytorium.

test12.yaml:
```bash
apiVersion: v1
kind: Pod
metadata:
  name: test12
spec:
  containers:
  - name: nginx-container
    image: damianboguszpl66/private:lab12
  imagePullSecrets:
  - name: my-dockerhub-secret
```
## 5. Utworzenie obiektu Secret, wykorzystującego token wygenerowany na DockerHub (zamiast hasła)

```bash
kubectl create secret docker-registry my-dockerhub-secret --docker-server=https://index.docker.io/v1/ --docker-username=damianboguszpl66 --docker-password=********************************************
```

![lab12 png4](img/png4.png)

```bash
kubectl get secret
```

Poniżej widać poprawnie utworzony obiekt Secret.

![lab12 png1](img/png6.png)


## 6. Utworzenie poda bazując na manifeście

```bash
kubectl apply -f test12.yaml
```

![lab12 png5](img/png5.png)

## 7. Sprawdzenie poprawności konfiguracji utworzonego poda

```bash
kubectl get pod
kubectl describe pod test12 
```

Na załączonym zdjęciu widać iż Pod działa poprawnie.

![lab12 png8](img/png8.png)

Poniżej widać iż Pod wykorzystuje poprawny obraz.

![lab12 png7](img/png7.png)

