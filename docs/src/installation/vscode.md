# Visual Studio Code

In the previous section, we installed the Julia programming language. It is possible to write Julia codes in any text editor and run them directly from the terminal. However, it is usually better to use some IDE that provides additional features such as syntax highlight or code suggestions. Several text editors provide Julia support. However, we recommend using [Visual Studio Code](https://code.visualstudio.com/). Visual Studio Code is a free source-code editor made by Microsoft that supports many programming languages (Julia, Python, ``\LaTex``, ...) via extensions. The editor is available on the official [download page](https://code.visualstudio.com/download).

![](vscodeinstall_1.png)

Download the proper installer, run it and follow the given instructions

![](vscodeinstall_2.png)

There is no need to change the default settings. On the installer's last window, select the `Launch Visual Studio Code` option and press the `Finish` button.

![](vscodeinstall_3.png)

If the installation was successful, the VS Code should open in a new window.

![](vscodeinstall_4.png)

## Extensions

If we want to use the VS Code as IDE for Julia, we have to install the Julia extension. It can be done directly from the VS Code. Open the `Extension MarketPlace` by pressing the button in the `Activity bar` (the left bar). Type `julia` in the search bar and select the Julia extension. Then press the `Install` button to install the extension.

![](vscodeext_1.png)

After the installation, press `Ctrl + Shift + P` to open the command palette. Type `Julia: Start REPL` into the command palette and press enter.

![](vscodeext_2.png)

The panel with the terminal and new Julia session should open.

![](vscodeext_3.png)

There are other useful extensions. We recommend installing the `Project Manager` extension that provides additional features to manage projects.

![](vscodeext_4.png)

We also recommend installing the `Bracket Pair Colorizer 2` extension. This extension allows matching brackets to be identified with colors.

![](vscodeext_5.png)