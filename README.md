# Początek

Tworzenie 

1) namespace zad4
```
kubectl create namespace zad4
```
2) każdy plik YAML możemy 'wywołać' za pomocą polecenia
```
kubectl apply -f <nazwa_pliku_yaml>.yaml 
```
3) każdy obiekt możemy podejrzeć:
```
kubectl describe <co?> <nazwa> -n <przestrzeń nazw>
```
Przykładowo:
```
kubectl describe quota res-quota -n zad5
kubectl describe hpa hpa.yaml
```

4) lub w inny sposób wszystkie obiekty:

```
kubectl get services -n zad4
kubectl get deploy -n zad4
kubectl get pods -n zad4
kubectl get hpa -n lab4
```

# Następnie

1) Po utworzeniu przestrzeni nazw zad5 dodałem ograniczenia (res-quota.yaml) - max 10 podów, max 2 CPU, max 1.5 Gi RAM. Ograniczenia te dotyczą każdego obiektu w przestrzeni nazw zad5.
2) Kolejny etap to utworzenie poda w przestrzeni nazw zad5, również ze swoimi ograniczeniami w pliku (nginx_pod.yaml).
3) Następnie utworzyłem serwis php-apache (Dep_Serv.yaml) w przestrzeni nazw zad4.
4) Kolejno został stworzony obiekt autoskalera (HorizontalPodAutoscaler) w pliku (hpa.yaml).
   W odniesieniu do pytania o maksymalną liczbę replik - utworzony w punkcie (2) pod może wykorzystać maksymalnie 200Mi, deployment stworzony w (3) ma liimit 250 Mi.
   Oznacza to, że skoro w przestrzeni nazw zad5 jest limi 1500Mi, to:
```
1500Mi = 200Mi +250Mi * x
x = 1300/250
x = 5.2
```

# Zatem maksymalnie można użyć 5 replik.

Następnie należy uruchomić load generatora wysyłającego żądania do php-apache:
```
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache.lab4.svc.cluster.local; done"
```

Potwierdzenie otrzymujemy, że serwis faktycznie się skaluje, po wydaniu polecenia:
```
kubectl get hpa php-apache --watch -n zad4
```
