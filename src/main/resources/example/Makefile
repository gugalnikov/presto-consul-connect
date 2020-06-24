.ONESHELL .PHONY: run-nomad-job run-connect-proxy register-presto-service test-connect-proxy purge-nomad-job

run-nomad-job:
	nomad job run presto-connect.hcl
run-connect-proxy:
	consul connect proxy -service prestopxy -upstream presto:8080 -log-level debug
register-presto-service:
	curl --request PUT --data @payload.json ${CONSUL_HTTP_ADDR}/v1/agent/service/register?replace-existing-checks=true
test-connect-proxy:
	curl -s http://127.0.0.1:8080/v1/info
purge-nomad-job:
	nomad stop -purge presto-connect | true
