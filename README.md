# smart-iiot

Based on flask & react.Supported mqtt & restful api,and have a beautiful dashboard.


## Quick Links:

- [Feature Roadmap](http://smartlinkcloud.com/smart-iiot-roadmap)
- [Feature Requests](https://discuss.smartlinkcloud.com/feature-requests)
- [Wechat Chat](252527676)
- [Documentation](http://smartlinkcloud.com/help/)
- [Blog](http://blog.smartlinkcloud.com/)
- [QQ Group](252527676)

## Deploy


### 1.verify_root


### 2.create_smart_iiot_user

    adduser --system --no-create-home --disabled-login --gecos "" smart_iiot

### 3.install_system_packages

	apt-get -y update
    # Base packages
    apt install -y python-pip python-dev nginx curl build-essential pwgen
    # Data sources dependencies:
    apt install -y libffi-dev libssl-dev libmysqlclient-dev libpq-dev freetds-dev libsasl2-dev
    # SAML dependency
    apt install -y xmlsec1
    # Storage servers
    apt install -y postgresql redis-server
    apt install -y supervisor

### 4.create_directories

    # mkdir -p $SMART-IIOT_BASE_PATH
    # chown smart_iiot $SMART-IIOT_BASE_PATH

    mkdir -p smart-iiot
    chown smart_iiot smart-iiot
    
    # Default config file
    if [ ! -f "$SMART-IIOT_BASE_PATH/.env" ]; then
        sudo -u smart_iiot wget "$FILES_BASE_URL/env" -O $SMART-IIOT_BASE_PATH/.env
    fi

    COOKIE_SECRET=$(pwgen -1s 32)
    echo "export SMART-IIOT_COOKIE_SECRET=$COOKIE_SECRET" >> $SMART-IIOT_BASE_PATH/.env


### 5.extract_smart-iiot_sources

    sudo -u smart_iiot wget "$LATEST_URL" -O "$SMART-IIOT_TARBALL"
    sudo -u smart_iiot mkdir "$VERSION_DIR"
    sudo -u smart_iiot tar -C "$VERSION_DIR" -xvf "$SMART-IIOT_TARBALL"
    ln -nfs "$VERSION_DIR" $SMART-IIOT_BASE_PATH/current
    ln -nfs $SMART-IIOT_BASE_PATH/.env $SMART-IIOT_BASE_PATH/current/.env

### 6.install_python_packages

    pip install --upgrade pip==9.0.3
    # TODO: venv?
    pip install setproctitle # setproctitle is used by Celery for "pretty" process titles
    pip install -r $SMART-IIOT_BASE_PATH/requirements.txt
    pip install -r $SMART-IIOT_BASE_PATH/requirements_all_ds.txt


### 7.create_database
    # Create user and database
    sudo -u postgres createuser smart_iiot --no-superuser --no-createdb --no-createrole
    sudo -u postgres createdb smart-iiot --owner=smart_iiot

    cd $SMART-IIOT_BASE_PATH
    sudo -u smart_iiot bin/run ./manage.py database create_tables


### 8.setup_supervisor
    wget -O /etc/supervisor/conf.d/smart-iiot.conf "$FILES_BASE_URL/supervisord.conf"
    service supervisor restart


### 9.setup_nginx
    rm /etc/nginx/sites-enabled/default
    wget -O /etc/nginx/sites-available/smart-iiot "$FILES_BASE_URL/nginx_smart-iiot_site"
    ln -nfs /etc/nginx/sites-available/smart-iiot /etc/nginx/sites-enabled/smart-iiot
    service nginx restart





