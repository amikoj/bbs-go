FROM golang:latest

ENV GOPROXY https://goproxy.cn
ENV GO111MODULE=on
ENV BBS_GO_PATH /data

WORKDIR ${BBS_GO_PATH}/
COPY go.* ${BBS_GO_PATH}/
RUN go mod download

ENV TIME_ZONE=Asia/Shanghai
RUN echo "${TIME_ZONE}" > /etc/timezone \
    && ln -sf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime

EXPOSE 8082 8082

COPY . ${BBS_GO_PATH}
WORKDIR ${BBS_GO_PATH}
CMD [ "go","run","main.go" ]