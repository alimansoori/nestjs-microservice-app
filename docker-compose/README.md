docker-compose -p microservice-app -f docker-compose/docker-compose.common.yml -f docker-compose/docker-compose.dev.yml up -d --build

docker-compose -p microservice-app -f docker-compose/docker-compose.common.yml -f docker-compose/docker-compose.prod.yml up -d --build

docker-compose -p microservice-app -f docker-compose/docker-compose.common.yml -f docker-compose/docker-compose.dev.yml down

docker-compose -p microservice-app -f docker-compose/docker-compose.common.yml -f docker-compose/docker-compose.prod.yml down

git submodule update --init --recursivecro