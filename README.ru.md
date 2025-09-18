# 🛠️ Руководства по системному администрированию

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black) ![Postfix](https://img.shields.io/badge/Postfix-Mail%20Server-blue?style=for-the-badge&logo=mail.ru) ![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge) ![Contributions Welcome](https://img.shields.io/badge/Contributions-Welcome-brightgreen?style=for-the-badge)

> **Язык**: [English](README.md) | Русский

Практические руководства для системных администраторов  
Решения, проверенные в production-среде

## 📚 Статьи

### 📧 Почтовые серверы
- [5 критических ошибок при настройке Postfix](postfix-critical-mistakes.md) 🔥  
  Основные проблемы конфигурации и их исправление
- [Почему письма попадают в спам после миграции сервера](why-your-emails-go-to-spam-after-server-migration.md)  
  Решение проблем с доставкой писем после переезда
- [Безопасность почтового сервера: Полное руководство](mail-server-security-guide.ru.md) ⚡  
  SPF, DKIM, DMARC, Postgrey, TLS и продвинутая защита

### 🔒 Безопасность
- Конфигурации безопасности и защита серверов
- fail2ban и обнаружение вторжений
- Лучшие практики SSL/TLS

### 📊 Мониторинг *(планируется)*  
- Zabbix: от установки до продвинутых триггеров
- Grafana + Prometheus для системного мониторинга

### 🌐 Web-серверы *(планируется)*
- Nginx: оптимизация производительности
- SSL/TLS: современные практики безопасности

## 💡 О проекте

Репозиторий содержит руководства для администраторов, которым нужны:

- ✅ **Проверенные решения** 
- ✅ **Production конфигурации** 
- ✅ **Пошаговые инструкции**
- ✅ **Быстрая диагностика**

## 🎯 Принципы

- **Практический подход** - никакой лишней теории
- **Production-тестировано** - конфиги из реальных проектов
- **Фокус на безопасность** - актуальные методы защиты
- **Готовая автоматизация** - скрипты в комплекте

## 🚀 Быстрый старт

```bash
# Клонируем репозиторий
git clone https://github.com/Anton-Babaskin/github.io-guides-.git
cd github.io-guides-

# Изучаем статьи
ls -la *.md
```

## 📋 Категории

| Категория | Описание | Статус |
|-----------|----------|--------|
| 📧 Mail Servers | Postfix, Dovecot, антиспам | ✅ В разработке |
| 🔒 Security | Защита серверов, мониторинг | ✅ В разработке |
| 📊 Monitoring | Zabbix, Grafana, алерты | 📋 Планируется |
| 🌐 Web Servers | Nginx, Apache, оптимизация | 📋 Планируется |
| 🐧 Linux Admin | Системное администрирование | 📋 Планируется |

## 🤝 Участие в проекте

Хотите улучшить руководства? Вот как это сделать:

### 🐛 Нашли проблему?
- Создайте [Issue](https://github.com/Anton-Babaskin/github.io-guides-/issues/new)
- Укажите конкретное руководство/раздел
- Скриншоты приветствуются

### 💡 Есть идеи?  
- Откройте [Discussion](https://github.com/Anton-Babaskin/github.io-guides-/discussions)
- Объясните, почему тема полезна
- Предложите структуру, если есть идеи

### ✍️ Хотите написать?
1. Fork репозитория
2. Создайте новую ветку:
   ```bash
   git checkout -b feature/new-article
   ```
3. Добавьте статью в markdown формате
4. Обновите README.md (добавьте ссылку в раздел "Статьи")
5. Создайте Pull Request

### 📝 Рекомендации по написанию
- Используйте эмодзи для заголовков
- Добавляйте блоки кода с подсветкой синтаксиса
- Включайте практические примеры
- Описывайте не только "как", но и "почему"
- Добавляйте troubleshooting секции

## 🌍 Доступные языки

- [**English**](README.md) - English version
- **Русский** (текущий)

## 📄 Лицензия

Этот проект распространяется под лицензией MIT.  
Подробности в файле [LICENSE](LICENSE).
