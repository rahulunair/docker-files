#Clear essentials

FROM clearlinux
#ENV http_proxy http://proxy-us.intel.com:911
#ENV https_proxy http://proxy-us.intel.com:912
RUN  swupd update && \
     swupd bundle-add performance-tools \
           editors \
	   python-extras \
	   python3-basic \
	   dev-utils

CMD [ "/bin/bash" ]

