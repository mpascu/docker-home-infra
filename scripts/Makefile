.PHONY: up down restart logs pull update status clean backup

# Start all services
up:
	docker compose up -d

# Stop all services
down:
	docker compose down

# Restart all services
restart:
	docker compose restart

# Follow logs for all services
logs:
	docker compose logs -f

# Follow logs for a specific service (usage: make log SERVICE=traefik)
log:
	docker compose logs -f $(SERVICE)

# Pull latest images
pull:
	docker compose pull

# Update: pull latest images and restart
update:
	docker compose pull
	docker compose up -d

# Show status of all containers
status:
	docker compose ps

# Remove stopped containers and unused images
clean:
	docker compose down --remove-orphans
	docker image prune -f

# Backup config folder (requires BACKUP_PATH env var)
backup:
	@if [ -z "$(BACKUP_PATH)" ]; then \
		echo "Error: BACKUP_PATH not set. Usage: make backup BACKUP_PATH=/path/to/backup"; \
		exit 1; \
	fi
	tar -czvf $(BACKUP_PATH)/config-backup-$$(date +%Y%m%d-%H%M%S).tar.gz ../config

# Show help
help:
	@echo "Available commands:"
	@echo "  make up       - Start all services"
	@echo "  make down     - Stop all services"
	@echo "  make restart  - Restart all services"
	@echo "  make logs     - Follow logs for all services"
	@echo "  make log SERVICE=name - Follow logs for specific service"
	@echo "  make pull     - Pull latest images"
	@echo "  make update   - Pull and restart services"
	@echo "  make status   - Show container status"
	@echo "  make clean    - Remove stopped containers and unused images"
	@echo "  make backup BACKUP_PATH=/path - Backup config folder"
