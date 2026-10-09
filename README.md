# Настраиваем центральный сервер для сбора логов<br><br>
Описание домашнего задания<br>
1.	Поднимаем две машины — web и log.
2.	На web поднимаем nginx.
3.	Настраиваем центральный лог-сервер на любой системе по выбору:<br>
•	journald;<br>
•	rsyslog;<br>
•	elk.<br>
4.	Настраиваем аудит, который будет отслеживать изменения конфигураций nginx.<br>
•	Все критичные логи с web должны собираться и локально и удаленно.<br>
•	Все логи с nginx должны уходить на удаленный сервер (локально только критичные).<br>
•	Логи аудита должны также уходить на удаленную систему.<br><br>
_______________________________________________________________________________________
1.	Машины поднимаем при помощи [Vagrantfile](https://github.com/mrigorevich/logs/blob/main/Vagrantfile) <br><br>
`sudo timedatectl set-timezone Europe/Moscow` # на каждой ВМ устанавливаем часовой пояс<br><br>
2.	На ВМ web:<br><br>
`sudo apt update && apt install -y nginx` # устанавливаем Nginx<br><br>
<img width="974" height="139" alt="image" src="https://github.com/user-attachments/assets/2a310551-9d4d-4f3a-a3c6-cb3fa4aea777" />
<img width="859" height="450" alt="image" src="https://github.com/user-attachments/assets/b08d0fb8-fd03-40de-bc70-f6ea4033accd" />
<br><br> 
3.	На ВМ log:

   
`apt list rsyslog` # проверяем установку rsyslog<br><br>
<img width="974" height="65" alt="image" src="https://github.com/user-attachments/assets/03075eda-8723-4971-8766-fd6d71a9ccb5" />
<br><br>
`nano /etc/rsyslog.conf` # включаем сбор логов<br><br>
<img width="650" height="308" alt="image" src="https://github.com/user-attachments/assets/2c5f4350-d1d3-4e76-b915-8e1d717582fd" />
<img width="974" height="115" alt="image" src="https://github.com/user-attachments/assets/dcfa762a-334a-4a29-bb38-51eaf0a9ee27" />
<br><br>
`systemctl restart rsyslog` # сохраняем файл с конфигом и перезапускаем сервис<br>
`ss -tulnp` # проверяем, слушаются ли нужные порты<br><br>
<img width="974" height="286" alt="image" src="https://github.com/user-attachments/assets/29ee87fa-16c5-4f94-85a3-955c2c1a9cc0" />
<br><br>
4.	На ВМ web:<br><br>
`nano /etc/nginx/nginx.conf` # настраиваем политику сохранения логов<br><br>
<img width="974" height="119" alt="image" src="https://github.com/user-attachments/assets/6829f73d-0839-4621-902c-c192d764a24d" />
<br><br>
`systemctl restart nginx` # перезапускаем nginx<br><br>
Проверяем:<br>
Зайдем в браузере несколько раз на валидный адрес.<br>
`chmod 640 /var/www/html/index.nginx-debian.html` # отключим права на просмотр дефолтной страницы для others и еще раз пробуем зайти в браузере<br><br>
На ВМ log:<br><br>
`cat /var/log/rsyslog/web/nginx_error.log`<br>
`cat /var/log/rsyslog/web/nginx_access.log`<br>
<img width="974" height="409" alt="image" src="https://github.com/user-attachments/assets/00677ded-d824-4e1f-8af3-6e14cc46436c" />
