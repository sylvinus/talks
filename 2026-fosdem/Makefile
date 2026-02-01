DOCKER_IMAGE = node:22-alpine
SLIDEV_PORT = 3030

.PHONY: dev build export pdf clean

# Start development server with hot reload
dev:
	docker run --rm -it \
		-v $(PWD):/app \
		-w /app \
		-p $(SLIDEV_PORT):$(SLIDEV_PORT) \
		$(DOCKER_IMAGE) \
		sh -c "npm install -g @slidev/cli && slidev --remote"

# Build static site
build:
	docker run --rm \
		-v $(PWD):/app \
		-w /app \
		$(DOCKER_IMAGE) \
		sh -c "npm install -g @slidev/cli && slidev build"

# Export to PDF (16:9 aspect ratio)
pdf:
	docker run --rm \
		-v $(PWD):/app \
		-w /app \
		$(DOCKER_IMAGE) \
		sh -c "npm install -g @slidev/cli playwright-chromium && npx playwright install chromium && slidev export --output slides.pdf --format pdf"

clean:
	rm -rf dist node_modules slides.pdf
