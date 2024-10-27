
ARG SEMAPHORE_VERSION=latest

FROM semaphoreui/semaphore:${SEMAPHORE_VERSION} as semaphoreui-user

ARG USER_ID=1001
ARG GROUP_ID=0

################################################################################
# Change semaphore user UID & GID

USER root

RUN if [ ${USER_ID:-0} -ne 0 ] || [ ${GROUP_ID:-0} -ne 0 ]; then \
    echo "Changing user to ${USER_ID}:${GROUP_ID}" &&\
    deluser semaphore &&\
    if getent group semaphore; then groupdel semaphore; fi &&\
    addgroup -g ${GROUP_ID} semaphore &&\
    adduser -G semaphore -u ${USER_ID} -s /bin/bash -D semaphore &&\
    adduser semaphore root &&\
    \
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /tmp/semaphore"                &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /tmp/semaphore                 &&\
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /etc/semaphore"                &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /etc/semaphore                 &&\
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /var/lib/semaphore"            &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /var/lib/semaphore             &&\
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /opt/semaphore"                &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /opt/semaphore                 &&\
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /usr/local/bin/server-wrapper" &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /usr/local/bin/server-wrapper  &&\
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /usr/local/bin/semaphore"      &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /usr/local/bin/semaphore       &&\
    echo "chown -f -h -R  ${USER_ID}:${GROUP_ID}  /home/semaphore"               &&\
          chown -f -h -R  ${USER_ID}:${GROUP_ID}  /home/semaphore                &&\
    \
    echo "User changed" \
;fi

USER semaphore
