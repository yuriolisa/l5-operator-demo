####
- Operator
- Operand (Our Application)

<aside class="notes">
  What is Level 2 ? On a basic level it can be described as being able to perform minor and patch upgrades. There are two layers two consider here. The Operator and the Operand i.e. our application. Operator upgrades can be configured to be done automatically via the Operator Lifecycle Manager. The version of the Operand is controlled by a field in our operators Custom Resource.
</aside>

---
####
![Free Lunch](images/freelunch.jpeg)

<aside class="notes">
  Economists like to say there is no such thing as a free lunch but in kubernetes you get a bunch of things for free and thats the sort of theme of level 2 and level 3.
</aside>

---
#### Minor versions
As easy as updating the image in our deployment

<aside class="notes"> 
  If we are talking about minor or patch upgrades, this is something we already get for free with kubernetes.
</aside>

---
#### Spec
```
apiVersion: pets.bestie.com/v1
kind: Bestie
metadata:
  name: bestie
spec:
  size: 3
  image: quay.io/opdev/bestie
  version: "1.3"
```

<aside class="notes">
  We can perform application updates by updating the our custom resource. Behind the scenes we use the kuberenetes client provided by the k8s controller runtime lib to update the deployment resource in kubernetes. Since the "user interface" of our operator is the CR, both the application image as well the version are exposed in the CR. Our controller gets the desired version from custom resource and update the pod template in the deployment image if current version is different than the desired version that we have set in our Spec.
</aside>

---
#### Status
```
Status:
  Appversion:  1.4
  Podstatus:
    bestie-app-7b44d67f9f-ns5bp : Running : quay.io/opdev/bestie:1.4
    bestie-app-7bd89d6bf4-vpjqh : Running : quay.io/opdev/bestie:1.3
    bestie-app-7b44d67f9f-4sxjr : Pending : quay.io/opdev/bestie:1.4
    bestie-app-7bd89d6bf4-wgrpk : Running : quay.io/opdev/bestie:1.3
```

<aside class="notes">
  Going with the theme of our custom resource being the interface to our application and operator combo, we can see the pods updating in the status field
</aside>

---
#### Seamless Upgrades
For minor / patch version updates liveness and readiness probes ensure that your image is rolled out only if it is healthy

<aside class="notes">
  This is another thing that we get for free with Kubernetes. Failing liveness probe will restart the container, whereas failing readiness probe will stop our application from serving traffic.
</aside>

---
#### Seamless but conditions apply*

<aside class="notes"> 
  There are scenarios where upgrades or more generally version changes may not be seamless. One example of this is incompatible changes.
</aside>

---
#### 
What if there are incompatible changes ?

![Incompatible Changes](images/incompatible_upgrade.png)

<aside class="notes"> 
  A bit more sophistication is needed to handle this kind of a scenario. So at the level 2 capability level upgrades will be seamless as long as there are no breaking database changes
</aside>
