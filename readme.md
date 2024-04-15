
### commands
```sh
# define project name
project=hotdeals
# build image
docker build -f .devcontainer/Dockerfile  -t $project-image .
# run docker
docker run -d --name $project -p 80:80 $project-image

# debug
docker run --name $project -p 80:80 $project-image
# remove docker
docker rm $project -f  
