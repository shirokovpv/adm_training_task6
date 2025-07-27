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
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">TEXT HERE</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">TEXT HERE</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">TEXT HERE</span></span></p>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">TEXT HERE</span></span></p>
