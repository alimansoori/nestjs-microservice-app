docker-compose -p microservice-app -f docker-compose/docker-compose.dev.yml up --build

docker-compose -p microservice-app -f docker-compose/docker-compose.dev.yml down

git submodule update --init --recursive