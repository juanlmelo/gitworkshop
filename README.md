# Git workshop
Usualmente el mejor lugar para encontrar documentacion es el sitio oficial, aqui podran encontrar un libro con suficiente informacion: https://git-scm.com/book/en/v2

La mejor manera de utilizar git es a traves de la linea de comandos, aunque hay herramientas fuera de git que complementan todo el ciclo de un merge request (MR)  durante el taller nos enfocaremos mayormente en como utilizar git de la linea de comandos y veremos un ejemplo de un MR utilizando gitlab.


## Resources
* [Basicos de Git](http://git-scm.com/book/en/v2/Getting-Started-Git-Basics)
* [Instalacion](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Configuracion de github](https://help.github.com/articles/set-up-git)
* [Ignorar archivos](https://git-scm.com/docs/gitignore)


* Algunos tips
```shell
# bash completion for git and shell coloring
# OsX
brew install bash-completion

#Add this to your ~/.bash_profile

YELLOW="\[\033[0;33m\]"

LIGHT_GRAY="\[\033[0;37m\]"

NO_COLOR="\[\033[0m\]"

if [ -f /usr/local/etc/bash_completion ]; then
     . /usr/local/etc/bash_completion
fi


function parse_git_branch () {
       git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}



PS1="$LIGHT_GRAY\u@\h$YELLOW:\w$NO_COLOR\$(parse_git_branch)$NO_COLOR\$ "
```

* Configuraciones globales

```shell
git config --global user.name "NOMBRE"
git config --global user.email "EMAIL"
git config --global color.ui auto
```

* Tambien se pueden editar las configuraciones en ~/.gitconfig
```shell
[user]
        name = jmelo
        email = jmelo@email.com
[branch]
        autosetuprebase = always # hacer rebase automaticamente al hacer pull
[push]
        default = tracking       # no hay necesidad de especificar el branch al hacer 'git push'
```

* Crear alias
```shell
# listar alias
git config --list | grep alias
# agregar alias
git config --global alias.last 'log -1 HEAD'
git config --global alias.lol "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```


* Crear repositorio
```shell
# Inicializar directorio en git
git init
# Proyecto existente
git clone https://github.com/juanlmelo/gitworkshop.git && cd gitworkshop
```

* Recording changes
```shell
# mostrar el status de los archivos actuales
git status
# agregar archivos a staging
git add .
git add myFile.txt
# commit
git commit -m "mensaje del commit"
```
* Sacar de staging (undo add)
```shell
git reset HEAD <file>...
```

* Deshacer cambios a un archivo modificado - DANGER
```shell
git checkout -- [file]...
```

* Regresar ultimo commit a staging
```shell
git reset --soft HEAD^
```

## Remotes
* [Introduccion](http://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)

En la mayoria de los casos se utiliza solo un repositorio remoto, son versiones de tu proyecto en el origen del repo.

```shell
# Listarlos
git remote -v
origin	https://github.com/juanlmelo/gitworkshop.git (fetch)
origin	https://github.com/juanlmelo/gitworkshop.git (push)
# Agregar un repositorio
git remote add gitw https://github.com/juanlmelo/gitworkshop
```

## Branching
* [Introduction](http://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)

Un 'Branch' es un apuntador a un commit en especifico, git no crea una copia del repositorio entero por lo que es mucho mas eficiente en cuanto a manejo del espacio y rapidez.


**HEAD es un apuntador al branch actual**

```shell
# Listar branches
git branches
# Crear un nuevo branch
git checkout -b testing
origin	git@github.com:juanlmelo/git-training.git (fetch)
origin	git@github.com:juanlmelo/git-training.git (push)
```

## Stash
* Toma los cambios en tu working directory o los archivos en staging y los coloca en un stack para su aplicacion en otro momento o en otra rama. 
```shell
git stash list # listar los elementos en stash
git stash      # coloca los cambios actuales en stash
git stash pop  # saca del stack los ultimos cambios ingresados al stack
git stash pop stash{2} #igual que el anterior pero la posicion 2 en el stack
git stash show -p # muestra el diff de el ultimo set de cambios guardados 
git stash show -p stash@{1} #igual que el anterior pero la posicion 1
```

## Watering & Merging
* [Merging basico](http://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
El proceso de hacer 'watering' se le llama cuando traemos los ultimos cambios de la rama padre hacia nuestro branch, es decir actualizamos nuestro branch con los ultimos commits.

```shell 
# watering
git checkout master
git pull
git checkout my-branch
git pull
git merge master
# arreglar cualquier conflicto
git push
```

Hacerle merge de un branch a master
```shell
git checkout master
git pull
git merge --no-commit --squash my-branch
# correr pruebas, arreglar conflictos
git commit -m "comentario"
git push

# eliminar tu rama
git branch -d my-branch
git push origin :my-branch
```


* merge conflicts

>Ocurre cuando se modifica la misma parte de un archivo en dos branches distintos los cuales estan en proceso de merge.

Lo ideal es utilizar un mergetool para hacer el proceso de arreglar conflictos.
Sugiero [kdiff3](http://kdiff3.sourceforge.net/) que es gratis, opensource y multiplataforma. Aunque yo utilizo intellij IDEA.



```shell
[mergetool "kdiff3"]

	cmd = /Applications/kdiff3.app/Contents/MacOS/kdiff3 \"$BASE\" \"$LOCAL\" \"$REMOTE\" -o \"$MERGED\"
	keepTemporaries = false
	trustExitCode = false
	keepBackup = false

[diff]

    tool = kdiff3

[difftool "kdiff3"]

	cmd = /Applications/kdiff3.app/Contents/MacOS/kdiff3 "\"$REMOTE\"" "\"$LOCAL\""
```

## Rebasing
* [Basic reabasing](http://git-scm.com/book/en/v2/Git-Branching-Rebasing)

# *Advertencia*
Hacer rebase reescribe la historia de git, significa que se les asignara un nuevo hash a tus commits despues del rebase.
Haz esto solo en tu propio branch y nunca en el branch principal (master).

>La manera mas facil de integrar tus cambios es con el comando `merge` como vimos anteriormente hay otra manera de hacer merge que es con rebase, con este comando puedes tomar todos tus cambios y hacerle replay en otro branch diferente.


## Ejemplo de rebase
Aqui usamos una estrategia llamada [Feature branch workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow)
Donde basicamente mantenemos un branch de master limpio (con pull reciente) creamos un branch de ahi y trabajamos en el. El proceso de watering es recomendado hacerlo diario.

Crear un branch de master
```shell
git checkout master
git pull
git checkout -b feature

# hacer un commit

git touch file.txt
git add .
git commit -m "add file"

# Si hay nuevos commits en master probablemente requieras integrar esos cambios en tu feature branch. Asi que lo que debemos hacer es tomar esos cambios y hacer rebase de tu branch feature en master.

git checkout master
git pull origin master
git checkout feature
git rebase master

# Si tienes conflictos git te lo dira, utiliza algun mergetool.

git mergetool

# Una vez resueltos los conflictos,  debemos indicar que deseamos continuar.

git rebase --continue

# Puedes cancelar el proceso de rebase ejecutando:

git rebase --abort

# Haz integrado los ultimos cambios de `master` en tu `feature` branch, ademas tus commits se han movido hacia el top de la historia.

# **Nota que esto ha cambiado el hash de tu commit, asi que si deseas hacer push deberas forzarlo.**

git push origin feature -f

# Antes de hacer un merge request es necesario hacer rebase a tu branch con el branch de `master`.
# Probablemente quieras limpiar los commits de tu branch de la siguiente manera:

git reset --soft HEAD~3

# Donde `3` es el numero de commits a hacer `squash` o comprimir.

# Eso quitara tus 3 ultimos commits y pondra los archivos afectados en tu working copy directory, es decir como si los acabaras de modificar.

# Solo necesitaras hacerle commit, y despues push como un solo commit al remote branch (con el flag de -f por que haz reescrito la historia).

#Aqui estaras listo para crear tu merge request ya que tu branch esta solo 1 commit adelante de `master`, debido a esto el merge sera fast-forward.
```






