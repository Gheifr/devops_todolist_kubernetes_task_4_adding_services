1. Створити другий под
Скопіюй існуючий YAML-файл todoapp-pod.yml, зміни в ньому ім’я пода на todoapp-1.

metadata:  
  `name: todoapp-1`
Застосуй файл:  
`kubectl apply -f todoapp-pod.yml -n mateapp`  
2. Перевірити статус подів  

`kubectl get pods -n mateapp`  
Очікувано: два поди з назвою todoapp та todoapp-1 мають статус Running.

3. Встановити namespace за замовчуванням

`kubectl config set-context --current --namespace=mateapp`
Тепер можна не вказувати -n todoapp у наступних командах.

4. 📦 Створити сервіс ClusterIP
У Visual Studio Code створити файл clusterIp.yml зі вмістом:  

`
apiVersion: v1
kind: Service
metadata:
  name: todoapp-service
  namespace: mateapp
spec:
  selector:
    app: todoapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
`

Застосувати файл:  

`kubectl apply -f clusterIp.yml`
5. 🔍 Перевірити створення сервісу  

`kubectl get svc` 
Переконайся, що зʼявився todoapp-service типу ClusterIP.

6. 🧪 Перевірити сервіс (2 способи)
✔ Спосіб 1: Port-forward

`kubectl port-forward service/todoapp-service 8081:80`
Після цього відкрий браузер на http://localhost:8081 — має завантажитись сторінка з Python-додатку.

✔ Спосіб 2: Через busybox
Підключитись до busybox:

`kubectl exec -it busybox -- sh`
Зробити HTTP-запит:  

`curl http://todoapp-service`
Очікувано: отримаєш HTML-вивід від Python-додатку.

7. Створити файл nodeport.yml  
Внести в нього дані для створення nope port:
```
apiVersion: v1
kind: Service
metadata:
  name: todoapp-nodeport-service
  namespace: mateapp
spec:
  type: NodePort
  selector:
    app: todoapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30007
```  
застосувати його:  

`kubectl apply -f nodeport.yml`

За допомогою:  
`kubectl get svc`  
переконатись, що node port запущено та протестувати в браузері перейшовши за адресою localhost на порту, вказаному в nodeport.yml.