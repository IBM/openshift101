

3. Add Kubernetes Resources

	* Create a new directory `v3/guestbook/helm/templates`,

		```console
		$ cd ../../v3/guestbook
		$ mkdir -p v3/guestbook/helm/templates
		```

	* To add a deployment resource, create a new file `v3/guestbook/helm/templates/deployment.yaml`,

		```yaml
		apiVersion: apps/v1beta2
		kind: Deployment
		metadata: 
		  name: guestbook-ui-deployment
		  namespace: guestbook-ns
		  labels: 
		    app: guestbook-ui
		spec:
		  template: 
			spec: 
			  containers:
			  - name: guestbook-ui
				image: <username>/guestbook-ui:1.0.0
				ports:
				- name: main
				  protocol: TCP
				  containerPort: 3000
		```

	* To add a service resource, create a new file `v3/guestbook/helm/templates/service.yaml`,

		```yaml
		apiVersion: v1
		kind: Service
		metadata:
		  name: guestbook-ui-svc
		  namespace: guestbook-ns
		  labels:
			app: guestbook-ui
		spec:
		  type: NodePort
		  ports:
		  - name: main
			protocol: TCP
			port: 3000
			targetPort: 3000
			nodePort: 32300		
		```