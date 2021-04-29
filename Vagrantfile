
Vagrant.configure("2") do |config|

  config.vm.box = "bento/ubuntu-18.04"
  config.vm.network "forwarded_port", guest: 80, host: 5000


  config.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get upgrade
      apt-get install -y python3-pip python3-venv python3-dev nginx

      mkdir /app
      cd /app
      python3 -m venv .venv
      source /app/.venv/bin/activate

      /app/.venv/bin/pip install wheel
      /app/.venv/bin/pip install flask gunicorn waitress
      git clone https://github.com/schumanm/flaskr.git
      cd flaskr/
      python setup.py bdist_wheel
      /app/.venv/bin/pip install dist/flaskr-1.0.0-py3-none-any.whl
      /app/.venv/bin/pip install -e .



      cp /app/flaskr/app.service /etc/systemd/system/
      cp /app/flaskr/app /etc/nginx/sites-available/
      privileged=false
      export FLASK_APP=flaskr
      flask init-db
      ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled
      rm /etc/nginx/sites-enabled/default
      chown vagrant:vagrant -R /app
      systemctl start app
      systemctl enable app
      nginx -s reload
    SHELL
end
