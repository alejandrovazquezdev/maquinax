# Manual Git y GitHub - Configuración Completa e Interactiva

## 📋 Estado Actual de mi Configuración (Verificado: 2025-08-24)

### ✅ Lo que YA tengo configurado:
- **Git instalado**: ✅ v2.39.5 (Apple Git-154)
- **Usuario configurado**: ✅ AleIsaac (alejandroisaacvazquezlopez@gmail.com)
- **Editor configurado**: ✅ VS Code (`code --wait`)
- **SSH keys creadas**: ✅ id_ed25519 (creada el 21 jun)
- **GitHub CLI autenticado**: ✅ Como alejandrovazquezdev con token activo
- **Configuraciones extra**: ✅ Alias, colores, gitignore global

### ⚠️ Lo que necesito ARREGLAR:
- **SSH keys no cargadas**: ❌ Agent sin identidades cargadas
- **Protocolo Git**: ⚠️ Usando HTTPS en lugar de SSH

---

## 🎯 Manual Paso a Paso

### Paso 1: Verificar Instalación de Git

#### ¿Qué verificar?
```bash
git --version
```

#### ¿Qué esperar?
- ✅ **SI aparece versión**: Git está instalado
- ❌ **SI dice "command not found"**: Necesitas instalar Git

#### Si necesitas instalar Git:
**macOS:**
```bash
# Opción 1: Con Homebrew (recomendado)
brew install git

# Opción 2: Descargar desde https://git-scm.com
```

**Windows:**
- Descargar desde: https://git-scm.com/download/win
- Durante instalación, acepta las opciones por defecto

---

### Paso 2: Configuración Inicial de Git

#### ¿Qué verificar?
```bash
git config --global --list
```

#### ¿Qué buscar?
- `user.name=TuNombre`
- `user.email=tuemail@ejemplo.com`

#### Si falta configuración:
```bash
# Configurar nombre y email
git config --global user.name "Tu Nombre Completo"
git config --global user.email "tuemail@ejemplo.com"

# Configuraciones adicionales recomendadas
git config --global core.editor "code --wait"  # Para VS Code
git config --global core.autocrlf input        # Para macOS/Linux
git config --global core.autocrlf true         # Para Windows
git config --global init.defaultBranch main
```

---

### Paso 3: Generar Claves SSH

#### ¿Qué verificar?
```bash
ls -la ~/.ssh/
```

#### ¿Qué buscar?
- `id_ed25519` (clave privada)
- `id_ed25519.pub` (clave pública)

#### Si no tienes claves SSH:
```bash
# Generar nueva clave SSH
ssh-keygen -t ed25519 -C "tuemail@ejemplo.com"

# Presiona Enter para aceptar la ubicación por defecto
# Puedes dejar la passphrase vacía o agregar una para mayor seguridad
```

---

### Paso 4: Agregar SSH Key al SSH Agent

#### ¿Qué verificar?
```bash
ssh-add -l
```

#### ¿Qué esperar?
- ✅ **SI aparece tu clave**: Está cargada
- ❌ **SI dice "no identities"**: Necesitas cargarla

#### Para cargar la clave:
```bash
# Iniciar SSH agent
eval "$(ssh-agent -s)"

# Agregar clave privada
ssh-add ~/.ssh/id_ed25519

# Verificar que se cargó
ssh-add -l
```

#### Para macOS - Configurar SSH permanente:
```bash
# Crear/editar archivo de configuración SSH
touch ~/.ssh/config

# Agregar al archivo ~/.ssh/config:
echo "Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519" >> ~/.ssh/config

# Agregar al keychain de macOS
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

---

### Paso 5: Agregar SSH Key a GitHub

#### Copiar clave pública:
```bash
# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Windows
clip < ~/.ssh/id_ed25519.pub

# Linux
cat ~/.ssh/id_ed25519.pub
# (copia manualmente el output)
```

#### En GitHub:
1. Ve a: **Settings** → **SSH and GPG keys**
2. Click **"New SSH key"**
3. **Title**: "Mi Mac" (o el nombre de tu computadora)
4. **Key type**: Authentication Key
5. **Key**: Pega la clave copiada
6. Click **"Add SSH key"**

#### Probar conexión:
```bash
ssh -T git@github.com
```

**Respuesta esperada:**
```
Hi usuario! You've successfully authenticated, but GitHub does not provide shell access.
```

---

### Paso 6: Configurar Git para usar SSH

#### ¿Qué verificar?
```bash
git config --global --get url."git@github.com:".insteadOf0
```

#### Si no está configurado:
```bash
# Configurar Git para usar SSH en lugar de HTTPS
git config --global url."git@github.com:".insteadOf "https://github.com/"
```

#### Para repositorios existentes:
```bash
# Cambiar remote de HTTPS a SSH
git remote set-url origin git@github.com:usuario/repositorio.git

# Verificar el cambio
git remote -v
```

---

### Paso 7: Instalar y Configurar GitHub CLI

#### ¿Qué verificar?
```bash
gh --version
```

#### Si no está instalado:
**macOS:**
```bash
brew install gh
```

**Windows:**
```bash
winget install --id GitHub.cli
```

#### Autenticarse:
```bash
gh auth login
```

**Seleccionar:**
1. **GitHub.com**
2. **SSH** (ya que configuramos SSH)
3. **Upload SSH public key**
4. **Login with a web browser**

#### Verificar autenticación:
```bash
gh auth status
```

### Método Alternativo: Personal Access Token (PAT)

#### Si prefieres usar token en lugar de SSH:

**1. Crear Personal Access Token en GitHub:**
- Ve a: **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**
- Click **"Generate new token"**
- **Expiration**: 90 days, 1 year, o sin expiración
- **Scopes**: Selecciona `repo`, `workflow`, `gist`
- **Guardar el token** (solo se muestra una vez)

**2. Configurar Git para usar token:**
```bash
# Configurar Git para usar HTTPS (en lugar de SSH)
git config --global credential.helper osxkeychain  # macOS
git config --global credential.helper manager-core # Windows

# No configurar la conversión SSH que hicimos antes
# Usar HTTPS normal para clonar
```

**3. Usar el token:**
```bash
# Al hacer git clone, push, pull por primera vez:
git clone https://github.com/usuario/repositorio.git

# Te pedirá:
# Username: tu-usuario-github  
# Password: [PEGAR AQUÍ EL TOKEN, no tu contraseña]
```

**4. GitHub CLI con token:**
```bash
gh auth login
# Seleccionar: GitHub.com → HTTPS → Paste authentication token
# Pegar tu PAT token
```

#### ¿Cuál método usar?

**SSH (Recomendado):**
- ✅ Más seguro
- ✅ No expira (a menos que cambies la clave)
- ✅ No necesitas recordar tokens

**Personal Access Token:**
- ✅ Más simple para principiantes
- ✅ Funciona en todas partes
- ⚠️ Expira y hay que renovarlo
- ⚠️ Si se compromete, acceso total a tu GitHub


### Paso 8: Configuraciones Adicionales Útiles

```bash
# Alias útiles
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --decorate --all"

# Configurar pull para hacer rebase
git config --global pull.rebase true

# Configurar colores
git config --global color.ui auto

# Editor predeterminado (si usas otro editor)
git config --global core.editor "nano"        # Para nano
git config --global core.editor "vim"         # Para vim
git config --global core.editor "subl -w"     # Para Sublime Text
```

---

## 🔧 Flujos de Trabajo Comunes

### Crear un nuevo repositorio:
```bash
# Localmente
mkdir mi-proyecto
cd mi-proyecto
git init
echo "# Mi Proyecto" > README.md
git add README.md
git commit -m "Initial commit"

# En GitHub (con CLI)
gh repo create mi-proyecto --public --source=. --remote=origin --push
```

### Clonar un repositorio:
```bash
# Con SSH (recomendado)
git clone git@github.com:usuario/repositorio.git

# Con GitHub CLI
gh repo clone usuario/repositorio
```

### Flujo básico de trabajo:
```bash
# 1. Verificar estado
git status

# 2. Agregar cambios
git add .

# 3. Hacer commit
git commit -m "Descripción de cambios"

# 4. Subir cambios
git push
```

---

## ✅ Lista de Verificación Final

- [ ] Git instalado y funcionando
- [ ] Usuario y email configurados
- [ ] SSH keys generadas
- [ ] SSH keys cargadas en SSH agent  ← **ESTO NECESITO ARREGLAR**
- [ ] SSH key agregada a GitHub
- [ ] Conexión SSH a GitHub funciona
- [ ] Git configurado para usar SSH  ← **ESTO NECESITO ARREGLAR**
- [ ] GitHub CLI instalado y autenticado
- [ ] Configuraciones adicionales aplicadas

---

## 🚨 Próximos Pasos para MI configuración:

1. **Cargar SSH key en el agent**
2. **Configurar Git para usar SSH por defecto**
3. **Probar conexión SSH a GitHub**
4. **Verificar que todo funciona creando un repo de prueba**

---

## 🔗 Trabajar con Repositorios Existentes

### Conectarse a un Repositorio que YA existe en GitHub

#### Caso 1: Repositorio vacío (recién creado en GitHub)
```bash
# 1. Clonar el repositorio vacío
git clone git@github.com:tu-usuario/nombre-repositorio.git

# 2. Entrar al directorio
cd nombre-repositorio

# 3. Crear archivos iniciales
echo "# Mi Proyecto" > README.md
echo "node_modules/" > .gitignore

# 4. Hacer el primer commit
git add .
git commit -m "Initial commit: Add README and gitignore"

# 5. Subir cambios
git push origin main
```

#### Caso 2: Repositorio que YA tiene contenido
```bash
# 1. Clonar el repositorio
git clone git@github.com:tu-usuario/nombre-repositorio.git

# 2. Entrar al directorio
cd nombre-repositorio

# 3. Verificar el estado actual
git status
git log --oneline -5  # Ver últimos 5 commits

# 4. Ya puedes trabajar normalmente
# Hacer cambios, commits y push como siempre
```

#### Caso 3: Conectar un proyecto local EXISTENTE a un repo de GitHub
```bash
# 1. Ir a tu proyecto local
cd /ruta/a/tu/proyecto-existente

# 2. Si no es un repo git, inicializarlo
git init

# 3. Agregar el remote de GitHub
git remote add origin git@github.com:tu-usuario/nombre-repositorio.git

# 4. Verificar la conexión
git remote -v

# 5. Si el repo de GitHub está vacío:
git add .
git commit -m "Initial commit"
git push -u origin main

# 6. Si el repo de GitHub YA tiene contenido:
git pull origin main --allow-unrelated-histories
# Resolver conflictos si los hay
git push origin main
```

### Flujo de Trabajo Diario

#### Una vez conectado al repositorio:
```bash
# 1. Siempre empezar sincronizando
git pull

# 2. Verificar estado
git status

# 3. Hacer cambios a tus archivos...

# 4. Ver qué cambió
git diff

# 5. Agregar cambios
git add .
# o específico: git add archivo.txt

# 6. Hacer commit
git commit -m "Descripción clara de los cambios"

# 7. Subir cambios
git push
```

### Comandos Útiles para Repos Existentes

#### Información del repositorio:
```bash
# Ver información del remote
git remote -v

# Ver todas las ramas
git branch -a

# Ver historial de commits
git log --oneline --graph --decorate

# Ver quién modificó qué
git blame archivo.txt
```

#### Trabajar con ramas:
```bash
# Crear y cambiar a nueva rama
git checkout -b nueva-funcionalidad

# Ver todas las ramas
git branch

# Cambiar de rama
git checkout main

# Subir nueva rama a GitHub
git push -u origin nueva-funcionalidad
```

#### Si algo sale mal:
```bash
# Deshacer último commit (pero mantener cambios)
git reset HEAD~1

# Descartar cambios no guardados
git checkout -- archivo.txt

# Volver a un commit específico
git reset --hard abc123

# Ver todo lo que has hecho
git reflog
```

### Conectarse con GitHub CLI (Alternativo)

```bash
# Si ya tienes GitHub CLI configurado:

# 1. Clonar cualquier repo tuyo
gh repo clone tu-usuario/nombre-repositorio

# 2. Ver tus repositorios
gh repo list

# 3. Crear nuevo repo y conectarlo al proyecto local
cd mi-proyecto-local
gh repo create nombre-nuevo-repo --source=. --remote=origin --push
```

---

## 📎 ANEXO: Métodos Menos Comunes (Para Casos Especiales)

### Autenticación sin navegador (servidores, headless):
```bash
# Con token desde archivo
echo "ghp_tu_token" > token.txt
gh auth login --with-token < token.txt
rm token.txt

# Con variable de entorno
export GH_TOKEN="ghp_tu_token"
# Ya funciona automáticamente

# Con token directo
echo "ghp_tu_token" | gh auth login --with-token
```

### Configurar Git para usar GitHub CLI (poco común):
```bash
# Paso 1: Primero tener GitHub CLI autenticado
gh auth login

# Paso 2: Hacer que Git use las credenciales de GitHub CLI
gh auth setup-git

# Verificar configuración
gh auth status
git config --get credential.helper
```

### Gestionar SSH keys desde CLI:
```bash
# Ver SSH keys en GitHub
gh ssh-key list

# Subir SSH key desde terminal
gh ssh-key add ~/.ssh/id_ed25519.pub --title "Mi servidor"

# Durante login inicial con SSH automático
gh auth login --git-protocol ssh
# Te pregunta si subir SSH key existente o crear nueva
```

**⚠️ Estos métodos son mucho menos comunes** - el flujo normal (SSH o PAT) cubre el 99% de casos.

---

*Manual creado: 2025-08-24*  
*Última verificación: 2025-08-24*
#github #github-cli #git #github-actions #inicio #configuracion 