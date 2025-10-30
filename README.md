# Домашнее задание к занятию 3 «Резервное копирование» - `Корниенко Кирилл`


### Задание 1

* Составьте команду rsync, которая позволяет создавать зеркальную копию домашней директории пользователя в директорию /tmp/backup
* Необходимо исключить из синхронизации все директории, начинающиеся с точки (скрытые)
* Необходимо сделать так, чтобы rsync подсчитывал хэш-суммы для всех файлов, даже если их время модификации и размер идентичны в источнике и приемнике.
* На проверку направить скриншот с командой и результатом ее выполнения
### *Решение:*

```
rsync -a --checksum --verbose --delete --progress --exclude '.*' /home/kirill/ /tmp/backup
```

![1](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync1.png)

![2](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync2.png)

![3](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync3.png)

![4](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync4.png)

![5](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync5.png)

![6](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync6.png)

![7](https://github.com/kirill-kornienko/Backup/blob/main/img/rsync7.png)

---

### Задание 2

* Написать скрипт и настроить задачу на регулярное резервное копирование домашней директории пользователя с помощью rsync и cron.
* Резервная копия должна быть полностью зеркальной
* Резервная копия должна создаваться раз в день, в системном логе должна появляться запись об успешном или неуспешном выполнении операции
* Резервная копия размещается локально, в директории /tmp/backup
* На проверку направить файл crontab и скриншот с результатом работы утилиты.


### *Решение*

####  файл crontab: [crontab](https://github.com/kirill-kornienko/Backup/blob/main/img/crontab)

```
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
05 16 * * * /bin/bash /home/kirill/every_day_backup.sh /home/kirill/ /tmp/backup
```

#### Результат работы утилиты:

![1](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161339.png)

![2](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161352.png)

![3](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161401.png)

![4](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161412.png)

![5](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161547.png)

![6](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161555.png)

![7](https://github.com/kirill-kornienko/Backup/blob/main/img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-10-30%20161618.png)


---
#### Скрипт:
```
#!/bin/bash
copy=$1
paste=$2

if [ ! -d $copy ]; then
	echo -e "\033[31m----- | $(date +"%Y-%m-%d | %H:%M:%S") | Backup completed Unsuccessfully! | Source directory does not exist. | -----\033[0m" >> /var/log/rsync-cron-$(date +"%Y-%m-%d").log
elif [ ! -d $paste ]; then
	echo -e "\033[31m----- | $(date +"%Y-%m-%d | %H:%M:%S") | Backup completed Unsuccessfully! | Destination directory does not exist. | -----\033[0m" >> /var/log/rsync-cron-$(date +"%Y-%m-%d").log
else
	rsync -a --checksum --verbose --delete --progress --exclude '.*' $copy $paste >> /var/log/rsync-cron-$(date +"%Y-%m-%d").log
  echo -e "\033[32m----- | $(date +"%Y-%m-%d | %H:%M:%S") | Backup completed Successfully! | -----\033[0m" >> /var/log/rsync-cron-$(date +"%Y-%m-%d").log
fi
```


