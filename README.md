# phpSysInfo-custom-plugin-statictext-guide

# Создание своего плагина для phpSysInfo (bootstrap режим)

Инструкция по созданию простого плагина для отображения статического текста/справки в phpSysInfo.

---

## Структура плагина

```
/usr/share/phpsysinfo/plugins/statictext/
├── class.statictext.inc.php      # PHP-класс плагина
├── statictext_bootstrap.html     # HTML-шаблон
├── js/
│   ├── statictext.js             # JS для dynamic режима
│   └── statictext_bootstrap.js   # JS для bootstrap режима (основной)
└── lang/
    └── en.xml                    # Языковой файл
```

---

## Шаг 1: Создать директории

```bash
mkdir -p /usr/share/phpsysinfo/plugins/statictext/js
mkdir -p /usr/share/phpsysinfo/plugins/statictext/lang
```

---

## Шаг 2: PHP-класс плагина

**Файл:** `/usr/share/phpsysinfo/plugins/statictext/class.statictext.inc.php`

```php
<?php
class statictext extends PSI_Plugin
{
    public function __construct($enc)
    {
        parent::__construct(__CLASS__, $enc);
    }
    
    public function execute()
    {
    }
    
    public function xml()
    {
        return $this->xml->getSimpleXmlElement();
    }
}
```

---

## Шаг 3: HTML-шаблон

**Файл:** `/usr/share/phpsysinfo/plugins/statictext/statictext_bootstrap.html`

```html
<div class="col-lg-12" id="block_statictext" style="display:none;">
    <div class="card" id="panel_statictext" style="display:none;">
        <div class="card-header">
            <span class="lang_plugin_statictext_001">My Services</span>
            <div id="reload_statictext" class="reload" title="reload"></div>
        </div>
        <div class="card-body" id="statictext-content">
        </div>
    </div>
</div>
```

---

## Шаг 4: JavaScript (bootstrap режим)

**Файл:** `/usr/share/phpsysinfo/plugins/statictext/js/statictext_bootstrap.js`

```javascript
function renderPlugin_statictext(data) {
    // === РЕДАКТИРУЙ ЭТОТ МАССИВ ===
    var lines = [
        'Обмен файлами - порт 8181 (сервис в докере)',
        'WebDav - порт 8080 (системный сервис)',
        '----',
        'Установлен сервис Tailscale',
        'Документация: http://example.com',
        '',
        'Любой твой текст тут'
    ];
    // ==============================
    
    var html = '<table class="table table-hover table-sm"><tbody>';
    
    for (var i = 0; i < lines.length; i++) {
        html += '<tr><td>' + (lines[i] || '&nbsp;') + '</td></tr>';
    }
    
    html += '</tbody></table>';
    
    $('#statictext-content').html(html);
    $('#block_statictext').show();
    $('#panel_statictext').show();
}
```

---

## Шаг 5: JavaScript (dynamic режим)

**Файл:** `/usr/share/phpsysinfo/plugins/statictext/js/statictext.js`

```javascript
function statictext(xml) {
    $("#Plugin_statictext").html("Static text plugin loaded");
    $("#Plugin_statictext").show();
}
```

---

## Шаг 6: Языковой файл

**Файл:** `/usr/share/phpsysinfo/plugins/statictext/lang/en.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tns:translationPlugin xmlns:tns="http://phpsysinfo.sourceforge.net/translation-plugin" lang="en">
    <expression id="plugin_statictext_001" name="statictext_title">
        <exp>My Services</exp>
    </expression>
</tns:translationPlugin>
```

---

## Шаг 7: Добавить конфигурацию

В конец файла `/etc/phpsysinfo/phpsysinfo.ini` добавь:

```ini
[statictext]
; StaticText Plugin configuration
ACCESS="data"
```

---

## Шаг 8: Включить плагин

В файле `/etc/phpsysinfo/phpsysinfo.ini` найди строку `PLUGINS=` и добавь `statictext`:

```ini
PLUGINS="statictext,Viewer,SMART,Docker"
```

---

## Шаг 9: Проверка

1. Очисти кэш браузера: `Ctrl+Shift+R`
2. Открой phpSysInfo
3. Блок "My Services" должен появиться

---

## Как редактировать текст

Открой файл:
```bash
nano /usr/share/phpsysinfo/plugins/statictext/js/statictext_bootstrap.js
```

Измени массив `lines`:
```javascript
var lines = [
    'Первая строка',
    'Вторая строка',
    '----',              // разделитель
    '',                  // пустая строка
    'Ещё текст'
];
```

Сохрани и обнови страницу.

---

## Как изменить заголовок блока

1. В `statictext_bootstrap.html` измени текст внутри `<span>`:
   ```html
   <span class="lang_plugin_statictext_001">Мой заголовок</span>
   ```

2. Или в `lang/en.xml`:
   ```xml
   <exp>Мой заголовок</exp>
   ```

---

## Расширенный вариант с HTML-форматированием

Если нужна более богатая разметка, замени содержимое `statictext_bootstrap.js`:

```javascript
function renderPlugin_statictext(data) {
    var html = '' +
        '<h5>🖥️ Сервисы</h5>' +
        '<ul class="list-group mb-3">' +
        '  <li class="list-group-item d-flex justify-content-between align-items-center">' +
        '    Nginx' +
        '    <span class="badge bg-success">работает</span>' +
        '  </li>' +
        '  <li class="list-group-item d-flex justify-content-between align-items-center">' +
        '    MySQL' +
        '    <span class="badge bg-success">активна</span>' +
        '  </li>' +
        '  <li class="list-group-item d-flex justify-content-between align-items-center">' +
        '    Redis' +
        '    <span class="badge bg-warning text-dark">перезапущен</span>' +
        '  </li>' +
        '</ul>' +
        '<h5>📋 Заметки</h5>' +
        '<p>WebDav доступен на порту 8080</p>' +
        '<p>Файловый обмен: порт 8181</p>' +
        '<hr>' +
        '<small class="text-muted">Обновлено: ' + new Date().toLocaleString() + '</small>';
    
    $('#statictext-content').html(html);
    $('#block_statictext').show();
    $('#panel_statictext').show();
}
```

---

## Устранение ошибок

| Ошибка | Решение |
|--------|---------|
| `JS-File for Plugin 'statictext' is missing!` | Создай файл `js/statictext.js` |
| `Config for plugin statictext not exist!` | Добавь секцию `[statictext]` в `phpsysinfo.ini` |
| Блок не появляется | Проверь что плагин в `PLUGINS=`, очисти кэш |
| Пустой блок | Проверь JavaScript в консоли браузера (F12) |

---

## Быстрая установка одной командой

```bash
# Создать все файлы плагина
mkdir -p /usr/share/phpsysinfo/plugins/statictext/{js,lang}

cat > /usr/share/phpsysinfo/plugins/statictext/class.statictext.inc.php << 'EOF'
<?php
class statictext extends PSI_Plugin
{
    public function __construct($enc)
    {
        parent::__construct(__CLASS__, $enc);
    }
    public function execute() {}
    public function xml()
    {
        return $this->xml->getSimpleXmlElement();
    }
}
EOF

cat > /usr/share/phpsysinfo/plugins/statictext/statictext_bootstrap.html << 'EOF'
<div class="col-lg-12" id="block_statictext" style="display:none;">
    <div class="card" id="panel_statictext" style="display:none;">
        <div class="card-header">
            <span class="lang_plugin_statictext_001">My Services</span>
            <div id="reload_statictext" class="reload" title="reload"></div>
        </div>
        <div class="card-body" id="statictext-content"></div>
    </div>
</div>
EOF

cat > /usr/share/phpsysinfo/plugins/statictext/js/statictext_bootstrap.js << 'EOF'
function renderPlugin_statictext(data) {
    var lines = [
        'Строка 1',
        'Строка 2',
        '----',
        'Строка 3'
    ];
    var html = '<table class="table table-hover table-sm"><tbody>';
    for (var i = 0; i < lines.length; i++) {
        html += '<tr><td>' + (lines[i] || '&nbsp;') + '</td></tr>';
    }
    html += '</tbody></table>';
    $('#statictext-content').html(html);
    $('#block_statictext').show();
    $('#panel_statictext').show();
}
EOF

cat > /usr/share/phpsysinfo/plugins/statictext/js/statictext.js << 'EOF'
function statictext(xml) {
    $("#Plugin_statictext").show();
}
EOF

cat > /usr/share/phpsysinfo/plugins/statictext/lang/en.xml << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<tns:translationPlugin xmlns:tns="http://phpsysinfo.sourceforge.net/translation-plugin" lang="en">
    <expression id="plugin_statictext_001" name="statictext_title">
        <exp>My Services</exp>
    </expression>
</tns:translationPlugin>
EOF

# Добавить конфиг (если ещё нет)
grep -q '\[statictext\]' /etc/phpsysinfo/phpsysinfo.ini || echo -e '\n[statictext]\nACCESS="data"' >> /etc/phpsysinfo/phpsysinfo.ini

echo "Готово! Добавь 'statictext' в PLUGINS= в /etc/phpsysinfo/phpsysinfo.ini"
```
