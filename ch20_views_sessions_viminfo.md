# Capítulo 20: Vistas, sesiones y Viminfo

Después de haber trabajado en un proyecto durante un tiempo, puedes encontrar que el proyecto de manera gradual va tomando forma con sus propios ajustes, carpetas, *buffers*, diseños, etc. Es como decorar tu apartamento después de vivir en el durante un tiempo. Es problema es, que cuando cierras Vim, pierdes esos cambios. ¿No sería genial si pudieras mantener esos cambios para que la próxima vez que abras Vim, pareciera que nunca lo hubieras cerrado?

En este capítulo, aprenderás a utilizar las vistas, sesiones y Viminfo para preservar una *instantánea" de tus proyectos.

## Vista (View)

Una vista es el subconjunto más pequeño de los tres temas de este capítulo (vista, sesiones, Viminfo). Es una colección de ajustes para una ventana. Si pasas mucho tiempo trabajando en una ventana y quieres preservar los mapas y carpetas, puedes utilizar una vista.

Vamos a crear un archivo llamado `foo.txt`:

```
foo1
foo2
foo3
foo4
foo5
foo6
foo7
foo8
foo9
foo10
```

En este archivo, creamos tres cambios:
1. En la línea 1, crea un pliegue de texto (fold) manual con `zf4j` (pliega el contenido de las cuatro líneas siguientes).
2. Cambia el ajuste de la numeración de las líneas `number`: `setlocal nonumber norelativenumber`. Esto eliminará los indicadores de número de la parte izquierda de la ventana.
3. Crea un mapeado local para bajar dos líneas cada vez que presiones `j` en vez de solo una línea, mediante `:nnoremap <buffer> j jj`.

Tu archivo ahora deberá tener un aspecto similar a este:

```
+-- 5 lines: foo1 -----
foo6
foo7
foo8
foo9
foo10
```

### Configurar los atributos de las vistas

Ejecuta:

```
:set viewoptions?
```

De manera predeterminada debería aparecer (aunque tu mensaje podría ser diferente dependiendo de tu archivo vimrc):

```
viewoptions=folds,cursor,curdir
```

Vamos a configurar `viewoptions`. Los tres atributos que quieres mantener son los pliegues de texto (folds), los mapeados y las opciones configuradas de manera local. Si tus ajustes se parecen a los míos, ya tienes la opción `folds`. Necesitas configurar las vistas para que recuerde las `localoptions`. Ejecuta:

```
:set viewoptions+=localoptions
```

Para aprender qué otras opciones están disponibles para `viewoptions`, echa un vistazo a `:h viewoptions`. Ahora si ejecutas `:set viewoptions?`, deberías ver algo así:

```
viewoptions=folds,cursor,curdir,localoptions
```

### Guardando las vistas

Con la ventana en la que aparece el archivo `foo.txt` correctamente plegada y teniendo las opciones de `nonumber norelativenumber`, vamos a guardas las vistas. Ejecuta:

```
:mkview
```

Vim crea un archivo de vistas.

### Archivos de vistas

Quizás te preguntes ¿dónde guardó Vim el archivo de las vistas? Para ver donde lo guardó Vim, ejecuta:

```
:set viewdir?
```

De manera predeterminada lo hace en esta ruta `~/.vim/view` (si tienes un sistema operativo diferente, podría mostrar una ruta diferente. Echa un vistazo a `:h viewdir` para más información). Si quieres cambiar esa ruta a otro sitio de tu sistema, añade esto en tu archivo de configuración vimrc:

```
set viewdir=$HOME/tu_ruta/personalizada
```

### Cargando el archivo de vistas

Cierra el archivo `foo.txt` si todavía no lo has hecho y después vuelve a abrirlo de nuevo. **Deberías ver el texto original sin los cambios.** Eso es lo esperado. 

Para restaurar el estado, necesitas cargar el archivo de vistas. Ejecuta:

```
:loadview
```

Ahora en tu archivo deberías ver:

```
+-- 5 lines: foo1 -----
foo6
foo7
foo8
foo9
foo10
```

Los pliegues de texto (folds), los ajustes locales y los mapeados locales se han recuperado. Si te das cuenta, también el cursor debería estar en la línea en la que estaba cuando ejecutaste `:mkview`. Siempre que tengas la opción `cursor`, las vistas también recuerdan la posición del cursor.

### Multiples vistas

Vim te permite guardar hasta un número de 9 vistas (1-9).

Supongamos que quieres hacer un pliegue (fold) adicional (por ejemplo quieres plegar texto de las dos últimas líneas) con `:9,10 fold`. Vamos a guardar esto como vista 1. Ejecuta:

```
:mkview 1
```

Si quieres hacer un pliegue de texto más con `:6,7 fold` y guardarlo como una vista diferente, ejecuta:

```
:mkview 2
```

Cierra el archivo. Cuando vuelvas a abrir `foo.txt` y quieras cargar la vista 1, ejecuta:

```
:loadview 1
```

Para cargar la vista 2, ejecuta:

```
:loadview 2
```

Para cargar la vista original, ejecuta:

```
:loadview
```

### Automatizar la creación de vistas

Una de las peores cosas que te pueden pasar trabajando con Vim es que después de pasar incontables horas organizando un archivo enorme con pliegues de texto, de manera accidental cierres la ventana y pierdas toda la información de los pliegues (folds). Para prevenir esto, quizás quieras crear automáticamente una vista cada vez que cierres un *buffer*. Para ello añade esto en tu archivo vimrc:

```
autocmd BufWinLeave *.txt mkview
```

de manera adicional, podría estar bien el cargar la vista cada vez que abres un *buffer*:

```
autocmd BufWinEnter *.txt silent loadview
```

Ahora no te tienes que preocupar de crear o cargar una vista nunca más cuando estés trabajando con archivos `txt`. Ten en cuenta que con el paso del tiempo, tu `~/.vim/view` podría empezar a acumular muchos archivos de vistas. Es buena idea limpiarlo cada pocos meses.

## Sessions

If a View saves the settings of a window, a Session saves the information of all windows (including the layout).

### Creating A New Session

Suppose you are working with these 3 files in a `foobarbaz` project:

Inside `foo.txt`:

```
foo1
foo2
foo3
foo4
foo5
foo6
foo7
foo8
foo9
foo10
```

Inside `bar.txt`:

```
bar1
bar2
bar3
bar4
bar5
bar6
bar7
bar8
bar9
bar10
```

Inside `baz.txt`:

```
baz1
baz2
baz3
baz4
baz5
baz6
baz7
baz8
baz9
baz10
```

Let's say that your windows layout look like the following (using strategically placed `split` and `vsplit`):

![Session Layout](images/session-layout.png)

To preserve this look, you need to save the Session. Run:

```
:mksession
```

Unlike `mkview` where it saves to `~/.vim/view` by default, `mksession` saves a Session file (`Session.vim`) in the current directory. Check out the file if you're curious what's inside.

If you want to save the Session file somewhere else, you can pass an argument to `mksession`:

```
:mksession ~/some/where/else.vim
```

If you want to overwrite the existing Session file, call the command with a `!` (`:mksession! ~/some/where/else.vim`).

### Loading A Session

To load a Session, run:

```
:source Session.vim
```

Now Vim looks like just the way you left it! Alternatively, you can also load a Session file from the terminal:

```
vim -S Session.vim
```

### Configuring Session Attributes

You can configure the attributes Session saves. To see what is currently being saved, run:

```
:set sessionoptions?
```

Mine says:

```
blank,buffers,curdir,folds,help,tabpages,winsize,terminal
```

If you don't want to save `terminal` when you save a Session, remove it from the session options. Run:

```
:set sessionoptions-=terminal
```

If you want to add an `options` when you save a Session, run:

```
:set sessionoptions+=options
```

Here are some attributes that `sessionoptions` can store:
- `blank` stores empty windows
- `buffers` stores buffers
- `folds` stores folds
- `globals` stores global variables (must start with an uppercase letter and contain at least one lowercase letter)
- `options` stores options and mappings
- `resize` stores window lines and columns
- `winpos` stores window position
- `winsize` stores window sizes
- `tabpages` stores tabs
- `unix` stores files in Unix format

For the complete list check out `:h 'sessionoptions'`.

Session is a useful tool to preserve your project's external attributes. However, some internal attributes aren't saved by Session, like local marks, registers, histories, etc. To save them, you need to use Viminfo!

## Viminfo

If you notice, after yanking a word into register a and quitting Vim, the next time you open Vim you still that text stored in the register. This is actually a work of Viminfo. Without it, Vim won't remember the register after you close Vim.

If you use Vim 8 or higher, Vim enables Viminfo by default, so you may have been using Viminfo this whole time without knowing it!

You might ask: "What does Viminfo save? How does it differ from Session?"

To use Viminfo, first you need to have `+viminfo` feature available (`:version`). Viminfo stores:
- The command-line history.
- The search string history.
- The input-line history.
- Contents of non-empty registers.
- Marks for several files.
- File marks, pointing to locations in files.
- Last search / substitute pattern (for 'n' and '&').
- The buffer list.
- Global variables.

In general, Session stores the "external" attributes and Viminfo the "internal" attributes.

Unlike Session where you can have one Session file per project, you normally will use one Viminfo file per computer. Viminfo is project-agnostic.

The default Viminfo location for Unix is `$HOME/.viminfo` (`~/.viminfo`). If you use a different OS, your Viminfo location might be different. Check out `:h viminfo-file-name`. Each time you make "internal" changes, like yanking a text into a register, Vim automatically updates the Viminfo file.

*Make sure that you have `nocompatible` option set (`set nocompatible`), otherwise your Viminfo will not work.*

### Writing And Reading Viminfo

Although you will use only one Viminfo file, you can create multiple Viminfo files. To write a Viminfo file, use the `:wviminfo` command (`:wv` for short).

```
:wv ~/.viminfo_extra
```

To overwrite an existing Viminfo file, add a bang to the `wv` command:

```
:wv! ~/.viminfo_extra
```

By default Vim will read from `~/.viminfo` file. To read from a different Viminfo file, run `:rviminfo`, or `:rv` for short:

```
:rv ~/.viminfo_extra
```

To start Vim with a different Viminfo file from the terminal, use the `i` flag:

```
vim -i viminfo_extra
```

If you use Vim for different tasks, like coding and writing, you can create a Viminfo optimized for writing and another for coding.

```
vim -i viminfo_writing

vim -i viminfo_coding
```

### Starting Vim Without Viminfo

To start Vim without Viminfo, you can run from the terminal:

```
vim -i NONE
```

To make it permanent, you can add this in your vimrc file:

```
set viminfo="NONE"
```

### Configuring Viminfo Attributes

Similar to `viewoptions` and `sessionoptions`, you can instruct what attributes to save with the `viminfo` option. Run:

```
:set viminfo?
```

You will get:

```
!,'100,<50,s10,h
```

This looks cryptic. Let's break it down:
- `!` saves global variables that start with an uppercase letter and don't contain lowercase letters. Recall that `g:` indicates a global variable. For example, if at some point you wrote the assignment `let g:FOO = "foo"`, Viminfo will save the global variable `FOO`. However if you did `let g:Foo = "foo"`, Viminfo will not save this global variable because it contains lowercase letters. Without `!`, Vim won't safe those global variables.
- `'100` represents marks. In this case, Viminfo will save the local marks (a-z) of the last 100 files. Be aware that if you tell Viminfo to save too many files, Vim can start slowing down. 1000 is a good number to have.
- `<50` tells Viminfo how many maximum lines are saved for each registers (50 in this case). If I yank 100 lines of text into register a (`"ay99j`) and close Vim, the next time I open Vim and paste from register a (`"ap`), Vim will only paste 50 lines max. If you don't give maximum line number, *all* lines will be saved. If you give it 0, nothing will be saved.
- `s10` sets a size limit (in kb) for a register. In this case, any register greater than 10kb size will be excluded.
- `h` disables highlighting (from `hlsearch`) when Vim starts.

There are other options that you can pass. To learn more, check out `:h 'viminfo'`.

## Using Views, Sessions, And Viminfo The Smart Way

Vim has View, Session, and Viminfo to take different level of your Vim environment snapshots. For micro projects, use Views. For larger projects, use Sessions. You should take your time to check out all the options that View, Session, and Viminfo offers.

Create your own View, Session, and Viminfo for your own editing style. If you ever need to use Vim outside of your computer, you can just load your settings and you will immediately feel at home!
