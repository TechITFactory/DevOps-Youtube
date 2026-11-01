# Videos

## Blue-Green Deployment in Kubernetes — definitions + complete live demo

**File:** `blue-green-deployment-kubernetes.mp4` (16 min, 1280x720, voice-over, chapters, burned-in captions)
**Subtitles:** `blue-green-deployment-kubernetes.srt`
**Lab files used in the demo:** `lab/` (`shop-blue.yaml`, `shop-green.yaml`, `service.yaml`, `preview.yaml`)

Udemy style: first the definitions on slides, then a console recording where **every command is explained before it runs, and every output is explained after it appears**. A small diagram next to the terminal shows which environment (blue or green) the Service sends users to, and it changes at the moment you run the switch.

### What you will learn
- What blue-green deployment is and why teams use it
- How Kubernetes does it with two Deployments, a label, and a Service selector (no special object needed)
- How to test the new (green) version privately before any user sees it
- How to switch all users with one `kubectl patch`, and roll back in seconds
- Blue-green vs rolling update vs canary, pros and cons, tips for real projects

### Chapters
| Time | Chapter |
|---|---|
| 00:00 | Welcome |
| 00:24 | Definitions: The problem: updating a live application |
| 00:59 | Definitions: What is blue-green deployment? |
| 01:36 | Definitions: How Kubernetes does it |
| 02:18 | Definitions: Key terms |
| 03:03 | Definitions: Blue-green vs rolling update vs canary |
| 03:43 | Definitions: Good to know before you use it |
| 04:17 | Definitions: What we will do in the terminal |
| 04:41 | Demo step 1: Check the cluster |
| 05:07 | Demo step 2: Create a namespace |
| 05:25 | Demo step 3: Set the default namespace |
| 05:42 | Demo step 4: Blue: the web page |
| 06:12 | Demo step 5: Blue: the Deployment manifest |
| 07:08 | Demo step 6: Blue: create it |
| 07:22 | Demo step 7: Blue: wait until ready |
| 07:46 | Demo step 8: Blue: look at the pods |
| 07:59 | Demo step 9: The Service: our switch |
| 08:23 | Demo step 10: Create the Service |
| 08:29 | Demo step 11: Inspect the Service |
| 08:43 | Demo step 12: Pod IP addresses |
| 09:03 | Demo step 13: The Service's endpoints |
| 09:19 | Demo step 14: A helper pod for testing |
| 09:33 | Demo step 15: Wait for the helper |
| 09:42 | Demo step 16: Act like a user |
| 10:04 | Demo step 17: Green: the new page |
| 10:20 | Demo step 18: Blue vs green: what differs? |
| 10:43 | Demo step 19: Green: create it |
| 10:50 | Demo step 20: Green: wait until ready |
| 11:01 | Demo step 21: Blue and green side by side |
| 11:22 | Demo step 22: Is green receiving users? |
| 11:45 | Demo step 23: A private door to green |
| 12:04 | Demo step 24: Create the preview Service |
| 12:10 | Demo step 25: Test green privately |
| 12:29 | Demo step 26: THE SWITCH: blue to green |
| 12:51 | Demo step 27: Users now get green |
| 13:06 | Demo step 28: Endpoints changed |
| 13:18 | Demo step 29: ROLLBACK: green back to blue |
| 13:31 | Demo step 30: Back on blue |
| 13:46 | Demo step 31: Switch to green again |
| 13:55 | Demo step 32: Green is live |
| 14:06 | Demo step 33: Retire the old version |
| 14:25 | Demo step 34: Only green remains |
| 14:35 | Demo step 35: Clean up |
| 14:55 | Demo step 36: Restore the default namespace |
| 15:03 | Recap |
| 15:27 | Tips for real projects |

### Definitions (from the video)
| Term | Meaning |
|---|---|
| **Pod** | The smallest unit: one or more containers running together. |
| **Deployment** | Keeps a set number of identical Pods running. |
| **Label** | A key=value tag on an object, like color=blue. |
| **Selector** | A filter that picks objects by their labels. |
| **Service** | One stable address for a group of Pods. Uses a selector. |
| **Endpoints** | The real Pod addresses behind a Service. Updated automatically. |
| **Readiness probe** | A health check. Only ready Pods receive traffic. |
| **Rollback** | Going back to the previous version. |

**Blue-green deployment:** two identical environments, blue (the version users have today) and green (the new version). Only one receives user traffic. You switch all traffic at once, and keep the old one running so you can switch back in seconds.

**In Kubernetes:** two Deployments (`shop-blue`, `shop-green`), every Pod labelled `color=blue` or `color=green`, and one Service whose selector (`color: blue` or `color: green`) is the switch.

### Commands in the demo (in order)
1. **Check the cluster**
   ```bash
   kubectl get nodes
   ```
2. **Create a namespace**
   ```bash
   kubectl create namespace bluegreen
   ```
3. **Set the default namespace**
   ```bash
   kubectl config set-context --current --namespace=bluegreen
   ```
4. **Blue: the web page**
   ```bash
   kubectl create configmap page-blue --from-literal=index.html='<h1>Shop v1 - BLUE</h1>'
   ```
5. **Blue: the Deployment manifest**
   ```bash
   cat shop-blue.yaml
   ```
6. **Blue: create it**
   ```bash
   kubectl apply -f shop-blue.yaml
   ```
7. **Blue: wait until ready**
   ```bash
   kubectl rollout status deployment/shop-blue
   ```
8. **Blue: look at the pods**
   ```bash
   kubectl get pods -L color
   ```
9. **The Service: our switch**
   ```bash
   cat service.yaml
   ```
10. **Create the Service**
   ```bash
   kubectl apply -f service.yaml
   ```
11. **Inspect the Service**
   ```bash
   kubectl get service shop
   ```
12. **Pod IP addresses**
   ```bash
   kubectl get pods -L color -o custom-columns=NAME:.metadata.name,COLOR:.metadata.labels.color,IP:.status.podIP
   ```
13. **The Service's endpoints**
   ```bash
   kubectl get endpoints shop
   ```
14. **A helper pod for testing**
   ```bash
   kubectl run tester --image=busybox:1.37 --command -- sleep 3600
   ```
15. **Wait for the helper**
   ```bash
   kubectl wait --for=condition=Ready pod/tester --timeout=60s
   ```
16. **Act like a user**
   ```bash
   for i in 1 2 3; do kubectl exec tester -- wget -qO- http://shop; echo; done
   ```
17. **Green: the new page**
   ```bash
   kubectl create configmap page-green --from-literal=index.html='<h1>Shop v2 - GREEN</h1>'
   ```
18. **Blue vs green: what differs?**
   ```bash
   diff shop-blue.yaml shop-green.yaml
   ```
19. **Green: create it**
   ```bash
   kubectl apply -f shop-green.yaml
   ```
20. **Green: wait until ready**
   ```bash
   kubectl rollout status deployment/shop-green
   ```
21. **Blue and green side by side**
   ```bash
   kubectl get pods -L color -o custom-columns=NAME:.metadata.name,COLOR:.metadata.labels.color,IP:.status.podIP
   ```
22. **Is green receiving users?**
   ```bash
   for i in 1 2 3; do kubectl exec tester -- wget -qO- http://shop; echo; done
   ```
23. **A private door to green**
   ```bash
   diff service.yaml preview.yaml
   ```
24. **Create the preview Service**
   ```bash
   kubectl apply -f preview.yaml
   ```
25. **Test green privately**
   ```bash
   kubectl exec tester -- wget -qO- http://shop-preview
   ```
26. **THE SWITCH: blue to green**
   ```bash
   kubectl patch service shop -p '{"spec":{"selector":{"color":"green"}}}'
   ```
27. **Users now get green**
   ```bash
   for i in 1 2 3 4; do kubectl exec tester -- wget -qO- http://shop; echo; done
   ```
28. **Endpoints changed**
   ```bash
   kubectl get endpoints shop
   ```
29. **ROLLBACK: green back to blue**
   ```bash
   kubectl patch service shop -p '{"spec":{"selector":{"color":"blue"}}}'
   ```
30. **Back on blue**
   ```bash
   for i in 1 2 3; do kubectl exec tester -- wget -qO- http://shop; echo; done
   ```
31. **Switch to green again**
   ```bash
   kubectl patch service shop -p '{"spec":{"selector":{"color":"green"}}}'
   ```
32. **Green is live**
   ```bash
   for i in 1 2 3; do kubectl exec tester -- wget -qO- http://shop; echo; done
   ```
33. **Retire the old version**
   ```bash
   kubectl scale deployment shop-blue --replicas=0
   ```
34. **Only green remains**
   ```bash
   kubectl get pods -L color
   ```
35. **Clean up**
   ```bash
   kubectl delete namespace bluegreen
   ```
36. **Restore the default namespace**
   ```bash
   kubectl config set-context --current --namespace=default
   ```

### Run it yourself
You need Docker, [kind](https://kind.sigs.k8s.io) and `kubectl`.
```bash
# Create a local cluster named lab
kind create cluster --name lab
# Go to the lab files
cd videos/lab
# Then follow the commands above, starting from step 2
```
The images are `nginx:1.28-alpine` and `busybox:1.37`.

### How the video was made
- Every command in the video was run for real on a kind cluster (Kubernetes v1.31.9). The outputs on screen are the real outputs (the namespace delete is sped up, it took about 40 seconds).
- The voice is a synthetic neural voice (Piper, `en_US-lessac-high`), so it can be regenerated or replaced.
- `src/` contains the scripts that capture the commands, render the frames and build the video (`capture.py`, `narr.py` for the narration script, `render.py`, `build.py`). They are provided for transparency, and use local paths from the recording machine.
