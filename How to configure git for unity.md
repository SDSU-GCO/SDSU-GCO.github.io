# How to configure git for unity

This is a guide for the initial configuration of git that GCO uses.  Please note that this guid is for github desktop with git cli and lfs support using UnityYamlMerge to resolve conflicts which falls back to tourtoise git.  

## Disclaimer!
This whole guide might not apply to everyone depending on your preffered tools and workflow, however feel free to use any parts of this guide.  This guide is provided under the understanding that we are not responsible if something goes wrong and we do not garentee the correctness/accuracy of this guide.

## Table of Contents

**Downloads and Installation**

[Installing Unity](https://sdsu-gco.github.io/How%20to%20configure%20git%20for%20unity.html#installing-unity)

[Installing Github Desktop](https://sdsu-gco.github.io/How%20to%20configure%20git%20for%20unity.html#installing-github-desktop)

[Installing Git CLI and LFS](https://sdsu-gco.github.io/How%20to%20configure%20git%20for%20unity.html#installing-git-cli-and-lfs)

**Configuration**

[Tool Configuration](https://sdsu-gco.github.io/How%20to%20configure%20git%20for%20unity.html#tool-configuration)

[Repository Configuration](https://sdsu-gco.github.io/How%20to%20configure%20git%20for%20unity.html#repository-configuration)



# Downloads and Installations

## Installing Unity

GCO recomends managing unity installs with unity hub.  If you know you don't use unity hub you can skip the Unity Hub step.

### Unity Hub

Visit [Download Unity Hub](https://unity3d.com/get-unity/download)and download Unity Hub. ***The download button is to the right of the standard unity install***.  Once downloaded launch the executable and follow the installation wizard.

![Unity Hub Download](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/UnityHubDownload.png)

### Unity Game Engine

Visit [Download Game Engine](https://unity3d.com/get-unity/download/archive)and download the appropriate version of Unity for your organization by clicking the "**Unity Hub**" button. ***In this case we will use Unity 2019.1.4f1 as our example***.  Allow the webpage to open Unity Hub.  Then simply complete the installation and follow any prompts from Unity Hub.

![Unity Game Engine Download](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/UnityGameEngineDownload.png)

## Installing Github Desktop
Visit [Download Github Desktop](https://desktop.github.com) and download the installer for your system and follow the installation wizard.
  
![Github Desktop](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/GithubDesktop.png)

## Installing Git CLI and LFS

### Git
Download then install [Git CLI](https://git-scm.com) ***Note that on windows some GCO members prefer to use git from CMD.EXE.  If you would like this make sure to select it at installation***

![Git CLI](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/GitCLI.png)

### LFS
Download then install [Git LFS](https://git-lfs.github.com).  This tool allows for large asset files such as sounds, images, and other large files to be stored more efficiently.

![Git LFS](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/GitLFS.png)

##

# Configuration

## Tool Configuration

Open your "**.gitconfig**" file and add these lines to it:
```ini

[merge]
    tool = unityyamlmerge

    [mergetool "unityyamlmerge"]
    trustExitCode = false
    cmd = 'C:/Program Files/Unity/Hub/Editor/2019.1.4f1/Editor/Data/Tools/UnityYAMLMerge.exe' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
```

## Repository Configuration

### LFS

This portion of the configuration must be completed for each repository.  First, launch the CLI

![Open CLI](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/OpenCLI.png)

Then Type `git lfs install` and hit enter as shown below:

![git install lfs](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/installGitLFS.png)

### .gitignore and .gitattributes

You should include a .gitignore to tell git not to sync unneccisary/temporary files and user specific files. You should also use a .gitattributes file to control what files are managed by lfs.  These are the [.gitignore(CLICK ME!)](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Files/gitignore.txt) and [.gitattributes(CLICK ME!)](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Files/gitattributes.txt) that GCO uses.  Simply rename them from "**gitignore.txt**" to "**.gitignore**" and from "**gitattributes.txt**" to "**.gitattributes**", then add them to the root directory of your repositoy(found by clicking show in explorer in github desktop) as shown:

![Open Repo Root Directory](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/showInExplorer.png)

![Add .gitignore and .gitAttributes](https://github.com/SDSU-GCO/SDSU-GCO.github.io/raw/master/Images/AddConfigFiles.png)


