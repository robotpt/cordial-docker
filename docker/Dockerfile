From osrf/ros:kinetic-desktop-full

# Set frontend to avoid debconf display errors
ENV DEBIAN_FRONTEND noninteractive

# Install essentials
RUN \
	apt-get update -y && apt-get upgrade -y \
	&& apt-get install -y \
		software-properties-common \
		apt-utils \
	&& apt-get update -y \
	&& apt-get install -y \
		curl \
		wget \
		tmux \
		zsh \
		curl \
		wget \
		vim \
		sudo \
		libgl1-mesa-glx \
		libgl1-mesa-dri \
		mesa-utils \
		unzip \
		terminator \
		git-gui \
		fonts-powerline \
		meld \
		nautilus \
		vlc \
	&& sed -i 's/geteuid/getppid/' /usr/bin/vlc \
	&& rm -rf /var/lib/apt/lists/* 

# Setup Pycharm
RUN \
	wget https://download.jetbrains.com/python/pycharm-community-2020.1.2.tar.gz \
	&& mv pycharm-community-* pycharm-community.tar.gz \
	&& tar xfz pycharm-*.tar.gz -C /opt/ \
	&& rm pycharm-community.tar.gz \
	&& echo 'alias pycharm="/opt/pycharm-community-2020.1.2/bin/pycharm.sh"' >> /root/.bashrc 

# Install Visual Studio Code
RUN \
	apt-get update -y \
	&& apt-get install -y \
		apt-transport-https \
		ca-certificates \
	&& curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg \
	&& mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg \
	&& sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list' \
	&& apt-get update -y \
	&& apt-get install -y code \
	&& rm -rf /var/lib/apt/lists/* 

# Setup new ROS key + install ros packages
RUN \
	apt-key del 421C365BD9FF1F717815A3895523BAEEB01FA116 \
	&& apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 \
	&& apt-get update -y && apt-get upgrade -y \
	&& apt-get install -y \
		ros-$ROS_DISTRO-ros-control \ 
		ros-$ROS_DISTRO-ros-controllers \ 
		ros-$ROS_DISTRO-gazebo-ros-pkgs \
		ros-$ROS_DISTRO-gazebo-ros-control \
		#ros-$ROS_DISTRO-rosbridge-server \
		ros-$ROS_DISTRO-audio-common \
		ros-$ROS_DISTRO-mongodb-store \
		ros-$ROS_DISTRO-mongodb-log \
	&& rm -rf /var/lib/apt/lists/* 

# Setup Python2 for cordial
RUN \
	apt-get update -y && apt-get upgrade -y \
	&& apt-get install -y \
		alsa \
		gstreamer1.0-alsa \
		vorbis-tools \
	&& apt-get install -y \
		python \
		python-pip \
		python-scipy \
		python-gst0.10 \
		python-mock \
		python-pyaudio \
	&& python2 -m pip install --upgrade pip \
	&& python2 -m pip install \
		rospkg \
		catkin_pkg \
		boto3==1.12.11 \
		soundfile==0.10.3.post1 \
		speedtest-cli==2.1.2 \
	&& rm -rf /var/lib/apt/lists/* 

# Setup HTTP server
RUN \
	apt-get update -y && apt-get upgrade -y \
	&& apt-get install -y \
		nodejs \
		npm \
	&& npm install http-server -g \
	&& ln -s /usr/bin/nodejs /usr/bin/node \
	&& rm -rf /var/lib/apt/lists/* 

# Setup Python3.6 with pip, virtualenv, and ROS
RUN \
	add-apt-repository -y ppa:deadsnakes/ppa \
	&& apt-get update -y && apt-get upgrade -y \
	&& apt-get install -y \
		wget \
		python3-pip \
		python3-yaml \
		python3-virtualenv \
		python3-vcstool \
	&& rm -rf /var/lib/apt/lists/* 

# Setup MongoDB
RUN \
	apt-get update -y && apt-get upgrade -y \
	&& apt-get install -y \
		python-pymongo \
		mongodb \
		scons \
	&& rm -rf /var/lib/apt/lists/* 


# Clone relevant repositories
#ENV commit_tag "abm_pilot"
# ENV git_options "-b ${commit_tag} --single-branch --recursive"
ENV git_options "--recursive"
RUN \
	mkdir -p /root/ws/catkin_ws/src \
	&& cd /root/ws/catkin_ws/src \
	&& git clone ${git_options} https://github.com/robotpt/qt-robot \
	&& git clone ${git_options} https://github.com/robotpt/cordial \
	&& git clone https://github.com/RobotWebTools/rosbridge_suite 


# Build repositories
RUN \
	cd /root/ws/catkin_ws \
	&& /bin/bash -c "source /opt/ros/kinetic/setup.bash && catkin_make"


# Put things you want in your ~/.bashrc here
RUN \
	unset DEBIAN_FRONTEND \
	&& echo " \
alias gs='git status'\n\
alias gl='git log --pretty=oneline --graph'\n\
alias gb='git branch -a'\n\
alias gg='git gui'\n\
alias gd='git diff HEAD'\n\
alias code='code --user-data-dir /root/.visual_code/'\n\
	" >> ~/.bashrc
