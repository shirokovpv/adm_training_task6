# adm_training_task6
<h1 align="center">Занятие 6. Размещаем свой RPM в своем репозитории</h1>
<h3 class="western"><a name="_heading=h.h6i87lkp3f19"></a> <span style="font-family: Roboto, serif;"><span style="font-size: small;">Описание домашнего задания</span></span></h3>
<p style="line-height: 100%; margin-bottom: 0cm;"><span style="font-family: Roboto, serif;">1) Создать свой RPM пакет</span></p>
<p style="line-height: 100%; margin-bottom: 0cm;"><span style="font-family: Roboto, serif;">2) Создать свой репозиторий и разместить там ранее собранный RPM</span></p>
<p style="line-height: 100%; margin-bottom: 0cm;">&nbsp;</p>
<h3 class="western"><a name="_heading=h.df570rpzx1qg"></a><span style="font-family: Roboto, serif;"><span style="font-size: small;">Используемые ОС</span></span></h3>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Виртуальная машина AlmaLinux 9.6. VirtualBox версия 7.0.8 r156879</span></span></p>
<p style="line-height: 100%; margin-bottom: 0cm;">&nbsp;</p>
<h3 class="western"><span style="font-family: Roboto, serif;"><span style="font-size: small;">Выполнение</span></span></h3>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">1) Создание RPM-пакета. Используем пакет веб-сервера Nginx и соберем его с дополнительным модулем ngx_brotli</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Вначале установим необходимые пакеты:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# yum install -y wget rpmdevtools rpm-build createrepo \
 yum-utils cmake gcc git nano</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Загрузим SRPM пакет веб-сервера Nginx для дальнейшей работы над ним:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# mkdir rpm && cd rpm</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training rpm]# yumdownloader --source nginx</span></span></p>
<img width="619" height="163" alt="image" src="https://github.com/user-attachments/assets/5846c51c-a157-463e-95e7-045f230411cf" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Далее поставим все зависимости для сборки пакета Nginx:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training rpm]# rpm -Uvh nginx*.src.rpm</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training rpm]# yum-builddep nginx</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Скачаем исходный код модуля ngx_brotli:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training rpm]# cd /root</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# git clone --recurse-submodules -j8 \
https://github.com/google/ngx_brotli</span></span></p>
<img width="644" height="372" alt="image" src="https://github.com/user-attachments/assets/b22f6f5b-d329-4d41-8ef7-6e2aa47c8463" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# cd ngx_brotli/deps/brotli</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training brotli]# mkdir out && cd out</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Собираем модуль ngx_brotli:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training out]# cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=OFF -DCMAKE_C_FLAGS="-Ofast -m64 -march=native -mtune=native -flto -funroll-loops -ffunction-sections -fdata-sections -Wl,--gc-sections" -DCMAKE_CXX_FLAGS="-Ofast -m64 -march=native -mtune=native -flto -funroll-loops -ffunction-sections -fdata-sections -Wl,--gc-sections" -DCMAKE_INSTALL_PREFIX=./installed ..</span></span></p>
<img width="644" height="466" alt="image" src="https://github.com/user-attachments/assets/267a9bf9-f886-4696-8f4c-79740379f3c7" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training out]# cmake --build . --config Release -j 2 --target brotlienc</span></span></p>
<img width="644" height="579" alt="image" src="https://github.com/user-attachments/assets/a7a84e5d-2e19-490d-bbaf-764df6301b00" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training out]# cd ../../../..</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Нужно поправить spec-файл, чтобы Nginx собирался с необходимыми нам опциями:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# nano /root/rpmbuild/SPECS/nginx.spec</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Добавим строку --add-module=/root/ngx_brotli \ в секцию configure и сохраним:</span></span></p>
<img width="639" height="650" alt="image" src="https://github.com/user-attachments/assets/86ecb064-3366-41cd-96b3-44fcbd142f72" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Теперь можно приступить к сборке RPM пакета:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# cd ~/rpmbuild/SPECS/</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training SPECS]# rpmbuild -ba nginx.spec -D 'debug_package %{nil}'</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Убедимся, что пакеты создались:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training SPECS]# ll ~/rpmbuild/RPMS/x86_64/</span></span></p>
<img width="644" height="309" alt="image" src="https://github.com/user-attachments/assets/847f39bd-73a2-4362-9962-ec94db09639c" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Копируем пакеты в общий каталог:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training SPECS]# cp ~/rpmbuild/RPMS/noarch/* ~/rpmbuild/RPMS/x86_64/</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training SPECS]# cd ~/rpmbuild/RPMS/x86_64</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Теперь можно установить наш пакет и убедиться, что nginx работает:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training x86_64]#  yum localinstall *.rpm</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training x86_64]# systemctl start nginx</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training x86_64]#  systemctl status nginx</span></span></p>
<img width="645" height="340" alt="image" src="https://github.com/user-attachments/assets/96dacab7-e547-45e3-a0ef-8ea85d29d8ab" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">2) Создать свой репозиторий и разместить там ранее собранный RPM</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Директория для статики у Nginx по умолчанию /usr/share/nginx/html. Создадим там каталог repo:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# mkdir /usr/share/nginx/html/repo</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Копируем туда наши собранные RPM-пакеты:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# cp ~/rpmbuild/RPMS/x86_64/*.rpm /usr/share/nginx/html/repo/</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Инициализируем репозиторий командой:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# createrepo /usr/share/nginx/html/repo/</span></span></p>
<img width="533" height="131" alt="image" src="https://github.com/user-attachments/assets/20f97b6e-0a4e-450b-a179-c5e7a0cba908" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Для прозрачности настроим в Nginx доступ к листингу каталога. В файле /etc/nginx/nginx.conf в блоке server добавим следующие директивы:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">index index.html index.htm;</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">autoindex on;</span></span></p>
<img width="539" height="298" alt="image" src="https://github.com/user-attachments/assets/cb295d22-9c7a-45f6-aba0-f69021143259" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Проверяем синтаксис и перезапускаем Nginx:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# nginx -t</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# nginx -s reload</span></span></p>
<img width="537" height="101" alt="image" src="https://github.com/user-attachments/assets/45c88642-2f67-4af1-822c-48c0e45f86d6" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Можно посмотреть с помощью curl:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# curl -a http://localhost/repo/</span></span></p>
<img width="643" height="513" alt="image" src="https://github.com/user-attachments/assets/c438fe7a-6490-4ab5-a7e9-7bb253841438" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Добавим наш репозиторий в /etc/yum.repos.d:</span></span></p>
<p>[root@training ~]# cat &gt;&gt; /etc/yum.repos.d/task6.repo &lt;&lt; EOF<br />[task6]<br />name=task6-linux<br />baseurl=http://localhost/repo<br />gpgcheck=0<br />enabled=1<br />EOF<br />[root@training ~]#</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Убедимся, что репозиторий подключился ( yum repolist enabled | grep task6):</span></span></p>
<img width="426" height="59" alt="image" src="https://github.com/user-attachments/assets/8a935fbc-9341-42f9-b84d-70da50c4d82a" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;"> Добавим пакет в наш репозиторий:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training ~]# cd /usr/share/nginx/html/repo/</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training repo]# wget https://repo.percona.com/yum/percona-release-latest.noarch.rpm</span></span></p>
<img width="642" height="305" alt="image" src="https://github.com/user-attachments/assets/fdea8fff-9df1-4a57-8600-bab7e32ad07b" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Обновим список пакетов в репозитории:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training repo]# createrepo /usr/share/nginx/html/repo/</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training repo]# yum makecache</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training repo]# yum list | grep task6</span></span></p>
<img width="613" height="275" alt="image" src="https://github.com/user-attachments/assets/d3c52c04-2756-4e81-9462-e5a08b8206ba" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">установим пакет percona-release:</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">[root@training repo]# yum install -y percona-release.noarch</span></span></p>
<img width="646" height="977" alt="image" src="https://github.com/user-attachments/assets/1e47c6c5-e3cf-42db-a587-a93c27346a26" />
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify">&nbsp;</p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Пакет установился из нашего репозитория.</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Задание завершено.</span></span></p>
