# renmawave-multistep-xraychecker-subpage-adaptive
Пошаговый шаблон для страницы подписки renmawave c интегрированным xray-checker 
<img width="1512" height="858" alt="Снимок экрана 2025-08-01 в 01 43 47" src="https://github.com/user-attachments/assets/f6c33d57-805f-466a-820e-7afee4cb4568" /><img width="1512" height="858" alt="Снимок экрана 2025-08-01 в 01 44 01" src="https://github.com/user-attachments/assets/3efe0a67-a010-49fe-9ec5-af99cb65a54e" /><img width="1512" height="858" alt="Снимок экрана 2025-08-01 в 01 44 28" src="https://github.com/user-attachments/assets/f472b786-6810-41d1-a365-510537529785" /><img width="1512" height="858" alt="Снимок экрана 2025-08-01 в 01 44 41" src="https://github.com/user-attachments/assets/116496e6-9837-4909-8f2d-df7fd919a928" /><img width="1512" height="858" alt="Снимок экрана 2025-08-01 в 01 44 56" src="https://github.com/user-attachments/assets/b201842b-e27e-4a37-85a2-7a889b7a5a1c" /><img width="1512" height="858" alt="Снимок экрана 2025-08-01 в 01 45 23" src="https://github.com/user-attachments/assets/ae45b875-8b4f-402f-9ff0-49c60d645712" /><img width="348" height="764" alt="Снимок экрана 2025-08-01 в 01 48 06" src="https://github.com/user-attachments/assets/6448168c-cd35-4718-beee-305f0e06836b" />


Приколюхи:

1) Выводит кол-во серверов и локаций из подписки и подтягивает к ним актуальный статус через (https://github.com/kutovoys/xray-checker)
2) Страница заточена под 4 вида устроств (Пк, IOS, Android, TV). Для TV на третьем шаге дополнительно скрывается кнопка подключающая подписку напрямую (Так как в моем варианте используется VPN4TV)
3) Интеграция с Telegram Mini App: Возможность использовать страницу подписки как Telegram Mini App в вашем боте.
(Переадресация/Redirect-страница: Возможность использовать собственную или внешнюю страницу переадресации (за основу взято: https://github.com/legiz-ru/Orion/blob/main/docs/redirect-page/index.html)
4) Относительно вылизанный адаптив под пк/телефон 

# Установка 

1) Поднимаем Xray-Checker https://github.com/kutovoys/xray-checker

2) Делаем обратное прокси на страницу /metrics (Скрываем страницу логином и паролем htpasswd для безопасности)

      
       location /metrics {
          auth_basic "Restricted Access";
          auth_basic_user_file /etc/nginx/.htpasswd;    

          proxy_pass http://host.docker.internal:2112/metrics;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;

          proxy_connect_timeout 5s;
          proxy_read_timeout 10s;
          proxy_send_timeout 5s;
          proxy_buffering off;

          add_header Access-Control-Allow-Origin *;
       }

3) В папку с remnawave закидывает index.html app-config.json. Создаем папку redirect-page в нее грузим redirect-page/index.html

   
4) Подключаем файл в докер файле в контейнер remnawave-subscription-page

         volumes:
           - ./index.html:/opt/app/frontend/index.html
           - ./app-config.json:/opt/app/frontend/assets/app-config.json
           - ./redirect-page:/opt/app/frontend/redirect-page

5)  В index.html меняем /metrics на страницу с метрикой, либо /metrics оставить, если панель и xray-chacker подняты на одном сервере. Прописываем логин/пасс от htpasswd, есди скрыли паролем.

                  // VPN Status Updates
        const USERNAME = "USERNAME";   // Юзернейм от htpasswd
        const PASSWORD = "PASSWORD";   // Пасс от htpasswd

        async function updateVpnStatus() {
            try {
                const basicAuth = 'Basic ' + btoa(`${USERNAME}:${PASSWORD}`);

                const res = await fetch('/metrics', {

6) Рестратим докер

        docker compose restart

# PS
Ни на какую гениальность не претендую. Программистом себя не считаю, делал это всё для себя. Делюсь, вдруг кому-то пригодится. Планирую добавить ещё пару приколюх, упростить скрытие/отображение блоков и кнопок.
