# travis-shared-configuration

Репозиторий для хранения общих конфигураций travis.  
  
Для подключения конфигураций пропишите в файл .travis.yml:  
import: yoomoney-gradle-plugins/travis-shared-configuration:<name>@master  
  
Для тестирования возможно указать feature ветку вместо @master.  
Подробнее о shared конфигурациях: https://docs.travis-ci.com/user/build-config-imports/#importing-configs-from-private-repositories  


## Описание конфигураций:
### build-and-publish-plugin.yml
Конфигурация создана для сборки, релиза и публикации gradle-plugin.  
Конфигурация объявляет stage для сборки проекта для всех веток, а также stage для выпуска релиза для ветки master.  
Используемые при релизе таски объявляются при подключении https://github.com/yoomoney-gradle-plugins/artifact-release-plugin.  
Для использования конфигурации необходимо дополнительно настроить travis для проекта по ссылке https://travis-ci.com/github/<account>/<repo>/settings:  
  
1. Генерируем пару ssh ключей для git пользователя, от имени которого будет выпускаться релиз. Публичную часть ключа необходимо
   загрузить в gitHub.  
   Приватную часть ключа необходимо зашифровать:  
   ```openssl aes-256-cbc -k GIT_PRIVATE_KEY_PASS -in git_key -out git_key.enc```  
   ```git_key.enc``` добавляем в репозиторий. Важно: git_key не добавляем в репозиторий и обозначем в .gitignore.

1. Прописать Environment Variables. Для возможности прописывать переменные, нужно быть членом организации (достаточно прав member).  
   Переменные не добавлены в файл конфигурации, т.к. при таком варианте использования сейчас 
   невозможно задавать переменные для конкретных веток, но в целях безопасности рекомендуется задавать их только для master ветки.  
   Переменные прописываются в блоке "Environment Variables", при добавлении переменной не забыть указать ветку master в блоке BRANCH.  
   Список необходимых переменных:  
   * Для загрузки артефакта в bintray.  
      Обязательные переменные, релиз будет выпущен, но артифакт в bintray не загрузится:  
      ```BINTRAY_API_KEY```  
      ```BINTRAY_PASSPHRASE```  
      ```BINTRAY_USER```

   * Для работы с git.  
      Применяется при выпуске релиза, обязательные переменные, без них релиз выпущен не будет:  
     ```GIT_KEY_PASSPHRASE```  
     ```GIT_PRIVATE_KEY_PASS```

   * Для работы с github.  
      Применяется при выпуске релиза, для добавления ссылки на pull-request в changelog, необязательная переменная,
      релиз выпустится успешно:  
      ```GITHUB_TOKEN```

   * Для публикации gradle-plugin на https://plugins.gradle.org.  
      Без переменных релиз будет выпущен, но плагин не будет загружен:  
      ```GRADLE_PUBLISH_KEY```  
      ```GRADLE_PUBLISH_SECRET```  
     
1. Добавить ssh key в разделе "SSH Key". Данный ключ будет использоваться при checkout репозитория.  
   Для успешного checkout прописывать ключ не требуется, но без ключа не выпускается релиз (не происходит успешный push).  
   Важно: пользователь, которому принадлежит ключ должен совпадать с пользователем, ключ которого добавляется в репозиторий.  
   На данном этапе требуется использовать ключ без passphrase (требование travis). Поэтому, если для пункта (1) генерировался ключ  
   с passphrase, здесь нужно сгенерировать отдельный ключ без passphrase (но для такого же пользователя) и использовать его.  