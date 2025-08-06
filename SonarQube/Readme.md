# SONARQUBE-SETUP
sudo apt update
sudo apt install openjdk-17-jdk unzip -y
java -version

sudo adduser sonar
sudo usermod -aG sudo sonar

cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.2.1.78527.zip
sudo unzip sonarqube-10.2.1.78527.zip
sudo mv sonarqube-10.2.1.78527 sonarqube
sudo chown -R sonar:sonar /opt/sonarqube

# Start as sonar user
sudo -u sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh start
