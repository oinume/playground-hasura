HASURA_CLI = ../bin/hasura
HASURA_CLI_INSTALL_PATH = $(shell dirname ${PWD})/bin

help: ## Show this help
	@perl -nle 'BEGIN {printf "Usage:\n  make \033[33m<target>\033[0m\n\nTargets:\n"} printf "  \033[36m%-15s\033[0m %s\n", $$1, $$2 if /^([a-zA-Z_-].+):.*\s+## (.*)/' $(MAKEFILE_LIST)
.PHONY: help

start: ## Start Hasura server
	@docker-compose up -d
.PHONY: start

start-and-migrate: start check-hasura migrate-and-metadata-apply ## Start Hasura server and run migrate/apply
.PHONY: start-and-migrate

stop: ## Stop Hasura server
	@docker-compose down
.PHONY: stop

stop-and-clean: ## Clean hasura server
	@docker-compose down -v
.PHONY: stop-and-clean

install-cli: ## Download hasura cli to current directory
	mkdir -p $(HASURA_CLI_INSTALL_PATH)
	curl -L https://github.com/hasura/graphql-engine/raw/stable/cli/get.sh | VERSION=v2.41.0 INSTALL_PATH=$(HASURA_CLI_INSTALL_PATH) bash
	@echo "hasura command is installed in $(HASURA_CLI_INSTALL_PATH)"
.PHONY: install-cli

metadata/%: ## Run metadata command with hasura-cli
	@$(HASURA_CLI) metadata $* --endpoint ${HASURA_ENDPOINT} --admin-secret ${HASURA_ADMIN_SECRET}

migrate/%: ## Run migrate command with hasura-cli
	@$(HASURA_CLI) migrate $* --endpoint ${HASURA_ENDPOINT} --admin-secret ${HASURA_ADMIN_SECRET} --database-name ${DATABASE_NAME}

migrate-and-metadata-apply: ## Run migrate apply and metadata apply
	$(MAKE) metadata/apply
	$(MAKE) migrate/apply
	$(MAKE) metadata/reload
.PHONY: migrate-and-metadata-apply

console: ## Open Hasura console
	@$(HASURA_CLI) console --endpoint ${HASURA_ENDPOINT} --admin-secret ${HASURA_ADMIN_SECRET}
.PHONY: console

db/connect:
	PGPASSWORD=${DATABASE_PASSWORD} psql -h ${DATABASE_HOST} -p ${DATABASE_PORT} -U ${DATABASE_USER} -d ${DATABASE_NAME}
.PHONY: db/connect

check-hasura:
	@sh ./check-hasura-alive.sh
