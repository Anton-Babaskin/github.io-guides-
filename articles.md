---
layout: default
title: "Все статьи"
description: "Полный каталог практических руководств по системному администрированию Linux. Почтовые серверы, безопасность, мониторинг, веб-серверы."
permalink: /articles/
---

# 📚 Все статьи

<div class="articles-filter">
  <div class="filter-buttons">
    <button class="filter-btn active" data-category="all">Все категории</button>
    <button class="filter-btn" data-category="mail-servers">📧 Почтовые серверы</button>
    <button class="filter-btn" data-category="security">🔒 Безопасность</button>
    <button class="filter-btn" data-category="monitoring">📊 Мониторинг</button>
    <button class="filter-btn" data-category="web-servers">🌐 Web-серверы</button>
  </div>
</div>

<div class="articles-grid">
  <!-- Published Articles -->
  <article class="article-card published" data-category="mail-servers">
    <div class="article-header">
      <span class="article-category mail-servers">📧 Почтовые серверы</span>
      <span class="article-difficulty intermediate">Средний уровень</span>
    </div>
    <h3 class="article-title">
      <a href="/guides/postfix-critical-mistakes/">5 критических ошибок при настройке Postfix</a>
    </h3>
    <p class="article-excerpt">
      Разбираем самые частые ошибки конфигурации, которые убивают репутацию домена и отправляют письма в спам. PTR-записи, SPF/DKIM/DMARC, Postscreen, Greylisting и мониторинг.
    </p>
    <div class="article-meta">
      <div class="article-stats">
        <span class="read-time">⏱️ 15 мин чтения</span>
        <span class="publish-date">📅 Август 2025</span>
        <span class="article-views">👁️ 1.2K просмотров</span>
      </div>
      <div class="article-tags">
        <span class="tag">Postfix</span>
        <span class="tag">Email Security</span>
        <span class="tag">Production</span>
        <span class="tag">Troubleshooting</span>
      </div>
    </div>
  </article>

  <!-- Coming Soon Articles -->
  <article class="article-card coming-soon" data-category="security">
    <div class="article-header">
      <span class="article-category security">🔒 Безопасность</span>
      <span class="article-status coming">Скоро</span>
    </div>
    <h3 class="article-title">
      Защита SSH: от базовых настроек до fail2ban
    </h3>
    <p class="article-excerpt">
      Комплексная защита SSH-доступа: настройка ключей, fail2ban, мониторинг подозрительной активности и автоматические алерты в Telegram.
    </p>
    <div class="article-meta">
      <div class="article-stats">
        <span class="read-time">⏱️ ~20 мин чтения</span>
        <span class="publish-date">📅 Сентябрь 2025</span>
        <span class="article-progress">🚧 В разработке (60%)</span>
      </div>
      <div class="article-tags">
        <span class="tag">SSH</span>
        <span class="tag">fail2ban</span>
        <span class="tag">Security</span>
        <span class="tag">Automation</span>
      </div>
    </div>
  </article>

  <article class="article-card coming-soon" data-category="monitoring">
    <div class="article-header">
      <span class="article-category monitoring">📊 Мониторинг</span>
      <span class="article-status planning">Планируется</span>
    </div>
    <h3 class="article-title">
      Zabbix: от установки до продвинутых триггеров
    </h3>
    <p class="article-excerpt">
      Полное руководство по настройке мониторинга инфраструктуры с Zabbix. Шаблоны, триггеры, алерты в Telegram и интеграция с Grafana.
    </p>
    <div class="article-meta">
      <div class="article-stats">
        <span class="read-time">⏱️ ~25 мин чтения</span>
        <span class="publish-date">📅 Октябрь 2025</span>
        <span class="article-progress">📋 В планах</span>
      </div>
      <div class="article-tags">
        <span class="tag">Zabbix</span>
        <span class="tag">Monitoring</span>
        <span class="tag">Grafana</span>
        <span class="tag">Telegram</span>
      </div>
    </div>
  </article>

  <article class="article-card coming-soon" data-category="web-servers">
    <div class="article-header">
      <span class="article-category web-servers">🌐 Web-серверы</span>
      <span class="article-status planning">Планируется</span>
    </div>
    <h3 class="article-title">
      Nginx: оптимизация производительности
    </h3>
    <p class="article-excerpt">
      Настройка высокопроизводительного Nginx: кэширование, сжатие, балансировка нагрузки, SSL/TLS оптимизация и мониторинг метрик.
    </p>
    <div class="article-meta">
      <div class="article-stats">
        <span class="read-time">⏱️ ~18 мин чтения</span>
        <span class="publish-date">📅 Ноябрь 2025</span>
        <span class="article-progress">💡 Идея</span>
      </div>
      <div class="article-tags">
        <span class="tag">Nginx</span>
        <span class="tag">Performance</span>
        <span class="tag">SSL/TLS</span>
        <span class="tag">Caching</span>
      </div>
    </div>
  </article>

  <article class="article-card coming-soon" data-category="monitoring">
    <div class="article-header">
      <span class="article-category monitoring">📊 Мониторинг</span>
      <span class="article-status planning">Планируется</span>
    </div>
    <h3 class="article-title">
      Prometheus + Grafana: современный стек мониторинга
    </h3>
    <p class="article-excerpt">
      Построение современной системы мониторинга: Prometheus для сбора метрик, Grafana для визуализации, Alertmanager для уведомлений.
    </p>
    <div class="article-meta">
      <div class="article-stats">
        <span class="read-time">⏱️ ~30 мин чтения</span>
        <span class="publish-date">📅 Декабрь 2025</span>
        <span class="article-progress">💡 Идея</span>
      </div>
      <div class="article-tags">
        <span class="tag">Prometheus</span>
        <span class="tag">Grafana</span>
        <span class="tag">Alertmanager</span>
        <span class="tag">DevOps</span>
      </div>
    </div>
  </article>

  <article class="article-card coming-soon" data-category="security">
    <div class="article-header">
      <span class="article-category security">🔒 Безопасность</span>
      <span class="article-status planning">Планируется</span>
    </div>
    <h3 class="article-title">
      Аудит безопасности Linux-сервера
    </h3>
    <p class="article-excerpt">
      Пошаговый чек-лист аудита безопасности: анализ уязвимостей, настройка файрволла, проверка прав доступа, мониторинг логов безопасности.
    </p>
    <div class="article-meta">
      <div class="article-stats">
        <span class="read-time">⏱️ ~22 мин чтения</span>
        <span class="publish-date">📅 2026</span>
        <span class="article-progress">💡 Идея</span>
      </div>
      <div class="article-tags">
        <span class="tag">Security Audit</span>
        <span class="tag">Firewall</span>
        <span class="tag">Log Analysis</span>
        <span class="tag">Compliance</span>
      </div>
    </div>
  </article>
</div>

## 📊 Статистика публикаций

<div class="publication-stats">
  <div class="stats-grid">
    <div class="stat-card">
      <div class="stat-number">1</div>
      <div class="stat-label">Опубликованные статьи</div>
    </div>
    <div class="stat-card">
      <div class="stat-number">5</div>
      <div class="stat-label">В разработке</div>
    </div>
    <div class="stat-card">
      <div class="stat-number">15+</div>
      <div class="stat-label">Минут среднее время чтения</div>
    </div>
    <div class="stat-card">
      <div class="stat-number">4</div>
      <div class="stat-label">Категории</div>
    </div>
  </div>
</div>

## 💡 Хотите предложить тему?

<div class="suggest-topic">
  <h3>Есть идея для статьи?</h3>
  <p>Если у вас есть интересная тема, с которой вы сталкивались в работе, или вопрос, который требует подробного разбора - напишите!</p>
  
  <div class="suggestion-buttons">
    <a href="https://github.com/Anton-Babaskin/guides/discussions/categories/ideas" class="btn btn-primary">
      💡 Предложить тему
    </a>
    <a href="https://github.com/Anton-Babaskin/guides/issues/new?template=article-request.md" class="btn btn-secondary">
      📝 Заказать статью
    </a>
  </div>
</div>

<style>
/* Articles Filter */
.articles-filter {
  margin: 2rem 0;
  text-align: center;
}

.filter-buttons {
  display: flex;
  justify-content: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.filter-btn {
  padding: 0.75rem 1.5rem;
  background: #f8f9fa;
  border: 2px solid #e1e4e8;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.2s;
  font-weight: 500;
  color: #24292e;
}

.filter-btn:hover,
.filter-btn.active {
  background: #0366d6;
  border-color: #0366d6;
  color: white;
  transform: translateY(-1px);
}

/* Articles Grid */
.articles-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 2rem;
  margin: 3rem 0;
}

.article-card {
  background: white;
  border: 1px solid #e1e4e8;
  border-radius: 12px;
  padding: 2rem;
  transition: all 0.3s;
  position: relative;
  overflow: hidden;
}

.article-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(90deg, #0366d6, #28a745, #ffc107, #dc3545);
  opacity: 0;
  transition: opacity 0.3s;
}

.article-card:hover::before {
  opacity: 1;
}

.article-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0,0,0,0.15);
}

.article-card.published {
  border-color: #28a745;
}

.article-card.coming-soon {
  background: #f8f9fa;
  opacity: 0.9;
}

.article-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.article-category {
  padding: 0.4rem 1rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
}

.article-category.mail-servers { background: #e3f2fd; color: #0366d6; }
.article-category.security { background: #e8f5e8; color: #28a745; }
.article-category.monitoring { background: #fff8e1; color: #f57c00; }
.article-category.web-servers { background: #ffebee; color: #dc3545; }

.article-difficulty, .article-status {
  padding: 0.3rem 0.8rem;
  border-radius: 15px;
  font-size: 0.8rem;
  font-weight: 500;
}

.article-difficulty.intermediate { background: #fff3cd; color: #856404; }
.article-status.coming { background: #d1ecf1; color: #0c5460; }
.article-status.planning { background: #d4edda; color: #155724; }

.article-title {
  margin-bottom: 1rem;
}

.article-title a {
  color: #24292e;
  text-decoration: none;
  font-weight: 600;
  font-size: 1.3rem;
  line-height: 1.4;
}

.article-title a:hover {
  color: #0366d6;
  text-decoration: underline;
}

.article-excerpt {
  color: #6a737d;
  line-height: 1.6;
  margin-bottom: 1.5rem;
  font-size: 0.95rem;
}

.article-meta {
  border-top: 1px solid #e1e4e8;
  padding-top: 1rem;
}

.article-stats {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  margin-bottom: 0.75rem;
  font-size: 0.85rem;
  color: #6a737d;
}

.article-views {
  color: #28a745;
  font-weight: 500;
}

.article-progress {
  color: #0366d6;
  font-weight: 500;
}

.article-tags {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}

.tag {
  background: #f1f3f4;
  color: #5f6368;
  padding: 0.3rem 0.6rem;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: 500;
}

/* Publication Stats */
.publication-stats {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 3rem 2rem;
  border-radius: 15px;
  margin: 4rem 0;
  text-align: center;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 2rem;
  margin-top: 2rem;
}

.stat-card {
  text-align: center;
}

.stat-number {
  font-size: 3rem;
  font-weight: 700;
  color: #ffd700;
  display: block;
  line-height: 1;
}

.stat-label {
  font-size: 1rem;
  margin-top: 0.5rem;
  opacity: 0.9;
}

/* Suggest Topic Section */
.suggest-topic {
  background: #f8f9fa;
  border: 1px solid #e1e4e8;
  border-radius: 12px;
  padding: 2.5rem;
  text-align: center;
  margin: 3rem 0;
}

.suggest-topic h3 {
  color: #24292e;
  margin-bottom: 1rem;
}

.suggest-topic p {
  color: #6a737d;
  max-width: 600px;
  margin: 0 auto 2rem;
  line-height: 1.6;
}

.suggestion-buttons {
  display: flex;
  justify-content: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.btn {
  display: inline-block;
  padding: 0.75rem 2rem;
  border-radius: 8px;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.2s;
  border: 2px solid transparent;
}

.btn-primary {
  background: #0366d6;
  color: white;
}

.btn-primary:hover {
  background: #0256cc;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(3,102,214,0.3);
}

.btn-secondary {
  background: #6f42c1;
  color: white;
}

.btn-secondary:hover {
  background: #5e35b1;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(111,66,193,0.3);
}

/* Responsive Design */
@media (max-width: 768px) {
  .filter-buttons {
    flex-direction: column;
    align-items: center;
  }
  
  .filter-btn {
    width: 100%;
    max-width: 300px;
  }
  
  .articles-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }
  
  .article-card {
    padding: 1.5rem;
  }
  
  .article-stats {
    flex-direction: column;
    gap: 0.5rem;
  }
  
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 1rem;
  }
  
  .stat-number {
    font-size: 2rem;
  }
  
  .suggestion-buttons {
    flex-direction: column;
    align-items: center;
  }
  
  .btn {
    width: 100%;
    max-width: 250px;
  }
}

@media (max-width: 480px) {
  .stats-grid {
    grid-template-columns: 1fr;
  }
  
  .publication-stats {
    padding: 2rem 1rem;
  }
}
</style>

<script>
// Articles filtering functionality
document.addEventListener('DOMContentLoaded', function() {
  const filterButtons = document.querySelectorAll('.filter-btn');
  const articles = document.querySelectorAll('.article-card');
  
  filterButtons.forEach(button => {
    button.addEventListener('click', function() {
      const category = this.getAttribute('data-category');
      
      // Update active button
      filterButtons.forEach(btn => btn.classList.remove('active'));
      this.classList.add('active');
      
      // Filter articles
      articles.forEach(article => {
        if (category === 'all' || article.getAttribute('data-category') === category) {
          article.style.display = 'block';
          setTimeout(() => {
            article.style.opacity = '1';
            article.style.transform = 'translateY(0)';
          }, 100);
        } else {
          article.style.opacity = '0';
          article.style.transform = 'translateY(20px)';
          setTimeout(() => {
            article.style.display = 'none';
          }, 300);
        }
      });
    });
  });
});
</script>
