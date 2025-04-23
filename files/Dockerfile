
ARG SEMAPHORE_VERSION=latest

FROM semaphoreui/semaphore:${SEMAPHORE_VERSION} as semaphoreui-user

ARG USER_ID=1001
ARG TZ=UTC

################################################################################
# Change semaphore user uid

USER root

RUN if [ ${USER_ID:-0} -ne 0 ] && [ ${USER_ID:-0} -ne 1001 ]; then \
    echo "Changing semaphore user uid to ${USER_ID}" &&\
    deluser semaphore &&\
    adduser -D -u ${USER_ID} -G root -s /bin/bash semaphore &&\
    \
    echo "chown -f -h -R  ${USER_ID}  /home/semaphore"               &&\
          chown -f -h -R  ${USER_ID}  /home/semaphore                &&\
    \
    echo "chown -f -h -R  ${USER_ID}  /tmp/semaphore"                &&\
          chown -f -h -R  ${USER_ID}  /tmp/semaphore                 &&\
    echo "chown -f -h -R  ${USER_ID}  /etc/semaphore"                &&\
          chown -f -h -R  ${USER_ID}  /etc/semaphore                 &&\
    echo "chown -f -h -R  ${USER_ID}  /var/lib/semaphore"            &&\
          chown -f -h -R  ${USER_ID}  /var/lib/semaphore             &&\
    echo "chown -f -h -R  ${USER_ID}  /opt/semaphore"                &&\
          chown -f -h -R  ${USER_ID}  /opt/semaphore                 &&\
    \
    echo "chown -f -h -R  ${USER_ID}  /etc/ansible/ansible.cfg"      &&\
          chown -f -h -R  ${USER_ID}  /etc/ansible/ansible.cfg       &&\
    \
    echo "chown -f -h -R  ${USER_ID}  /usr/local/bin/server-wrapper" &&\
          chown -f -h -R  ${USER_ID}  /usr/local/bin/server-wrapper  &&\
    echo "chown -f -h -R  ${USER_ID}  /usr/local/bin/semaphore"      &&\
          chown -f -h -R  ${USER_ID}  /usr/local/bin/semaphore       &&\
    \
    echo "User changed" \
;fi

# Set system timezone
RUN if [ -f "/usr/share/zoneinfo/${TZ}" ]; then \
    echo "Setting timezone: ${TZ}"; \
    ln -sf "/usr/share/zoneinfo/${TZ}" /etc/localtime; \
fi

USER semaphore
