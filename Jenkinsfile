name: React App CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Set up Docker
        uses: docker/setup-buildx-action@v3

      - name: Build Docker Compose Services
        run: docker-compose up -d --build

      - name: Wait for React App to Start
        run: |
          echo "Waiting for app to start..."
          sleep 15
          curl --retry 10 --retry-delay 5 --retry-connrefused http://localhost:5173 || exit 1

      - name: Run Tests (Optional)
        run: |
          echo "You can add your test commands here, e.g. npm run test"
          docker-compose exec react-app npm run test

      - name: Shutdown Docker
        if: always()
        run: docker-compose down
