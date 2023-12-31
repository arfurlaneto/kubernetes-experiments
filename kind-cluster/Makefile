CLUSTER_NAME=mycluster

cluster-create:
	kind create cluster --config=kind/kind.yaml --name=${CLUSTER_NAME}
	kubectl config use-context kind-${CLUSTER_NAME}

cluster-destroy:
	kind delete clusters ${CLUSTER_NAME}

cluster-load-images: app-build
	kind load docker-image arfurlaneto/goserver:1 -n ${CLUSTER_NAME}
	kind load docker-image arfurlaneto/goserver:2 -n ${CLUSTER_NAME}
	kind load docker-image arfurlaneto/goserver:3 -n ${CLUSTER_NAME}
	kind load docker-image arfurlaneto/goserver:latest -n ${CLUSTER_NAME}

k8s-setup:
	kubectl apply --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
	kubectl wait --namespace ingress-nginx \
		--for=condition=ready pod \
		--selector=app.kubernetes.io/component=controller \
		--timeout=90s
	kubectl apply -f k8s

k8s-port-forward:
	kubectl port-forward service/goserver-service 8080:80

app-build:
	docker build --build-arg APP_VERSION=1.0 -t arfurlaneto/goserver:1 .
	docker build --build-arg APP_VERSION=2.0 -t arfurlaneto/goserver:2 .
	docker build --build-arg APP_VERSION=3.0 -t arfurlaneto/goserver:3 -t arfurlaneto/goserver:latest .

app-run:
	docker run --rm -p 80:8000 arfurlaneto/goserver:latest

app-request:
	curl http://localhost:80

fortio-run:
	kubectl run -it fortio --rm --image=fortio/fortio -- load -qps 800 -t 120s -c 70 "http://goserver-service/healthz"
