## ✅ Working Setup: Power Platform CLI on Pop!_OS (Feb 2026)

### 💀 Linux user working with f̶u̶c̶k̶i̶n̶ Microsoft here.
>I got stuck on a project where I had to learn Power Apps—and that f̶u̶c̶k̶i̶n̶ interface was killing me. First came Dataverse and database setup. Then the worst part: frontend "development." Jesus Christ, I barely know HTML. Just kidding—the drag-and-drop editor drove me mad.
So I dove into the void, searching for a way to actually write code—React, anything. That's when I found it: Code Components (aka Power Apps CLI + Fluent UI). We were running out of time; one screen was taking a full day to build with the visual editor.
Problem? Every setup tutorial assumed Windows. Obviously.
But I take the philosophy "where there's a shell, there's a way" way too seriously. After hours of fighting npm, Node versions, and whipping poor Qwen AI into submission, I finally got it working on Linux.
Would've been easier to just read the docs? Probably. But we all know how the real world works, right?
Enjoy these f̶u̶c̶k̶i̶n̶g 4 steps.

### Step 1: Install f̶u̶c̶k̶i̶n̶ .NET 10 via Microsoft's Script Installer

_(APT repos don't have .NET 10 for Ubuntu 22.04 yet – this bypasses that limitation)_
*Im using zshell, if youre using  bash change the commands with .zshrc to .bashrc*

```sh
# Download installer
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
chmod +x dotnet-install.sh

# Install .NET 10 to user directory (~/.dotnet10)
./dotnet-install.sh --channel 10.0 --install-dir ~/.dotnet10

# Cleanup installer
rm dotnet-install.sh
```
### Step 2: Configure the f̶u̶c̶k̶i̶n̶ Environment Variables

```sh
# Add to PATH and set DOTNET_ROOT permanently
cat >> ~/.zshrc << 'EOF'
export DOTNET_ROOT="$HOME/.dotnet10"
export PATH="$HOME/.dotnet10:$PATH"
EOF

# Apply immediately
source ~/.zshrc

# Verify .NET 10 is active
dotnet --list-runtimes | grep "Microsoft.NETCore.App 10"
# Should show: Microsoft.NETCore.App 10.0.x [/home/youruser/.dotnet10/shared/Microsoft.NETCore.App]
```

### Step 3: Install the f̶u̶c̶k̶i̶n̶ Power Platform CLI v2.1.2

```sh 
# Create install directory
mkdir -p ~/powerplatform && cd ~/powerplatform

# Download Linux binary from NuGet (official source)
wget https://www.nuget.org/api/v2/package/Microsoft.PowerApps.CLI.Core.linux-x64/2.1.2 -O pac.zip

# Extract (NuGet packages are ZIP archives)
unzip -q pac.zip

# Make pac executable (keep it INSIDE tools/ with its dependencies!)
chmod +x tools/pac

# Add tools/ directory to PATH permanently
echo 'export PATH="$HOME/powerplatform/tools:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Step 4: Verify It f̶u̶c̶k̶i̶n̶g Works

```sh
pac --version
```

✅ **Expected output:**

```sh
Microsoft PowerPlatform CLI
Version: 2.1.2+g...
```

---

## 🔑 Critical Success Factors (Why This Works)

| Mistake to Avoid             | Why It Fails                                   | Fix                                                  |
| ---------------------------- | ---------------------------------------------- | ---------------------------------------------------- |
| Using APT for .NET 10        | Not available in Ubuntu 22.04 repos yet        | **Use script installer** → `~/.dotnet10`             |
| Missing `DOTNET_ROOT`        | .NET can't locate runtime                      | **Set `DOTNET_ROOT=~/.dotnet10`**                    |
| Using GitHub releases        | Microsoft doesn't publish Linux binaries there | **Download from NuGet CDN** (`api/v2/package/...`)   |

---

## 💡 Usage Tips

1. **Authenticate with Power Platform:**

```sh
pac auth create --url https://<your-env>.crm.dynamics.com
```

2. **Use in VS Code:**
    - Install **Power Platform Tools** extension (`microsoft-isvexpt.powerplatform-vscode`)
    - Open terminal → run `pac` commands directly
    - No Windows container needed!

3. **Update CLI later:**

```sh
# Just re-download newer version to ~/powerplatform/
cd ~/powerplatform
wget https://www.nuget.org/api/v2/package/Microsoft.PowerApps.CLI.Core.linux-x64/<new-version> -O pac.zip
rm -rf tools && unzip -q pac.zip && chmod +x tools/pac
```

---

## ⚠️ Troubleshooting Checklist

If `pac --version` fails:

```sh
# 1. Verify .NET 10 is active
dotnet --list-runtimes | grep 10

# 2. Verify pac and dependencies are together
ls -la ~/powerplatform/tools/pac* 
# Must show: pac, pac.dll, pac.runtimeconfig.json

# 3. Verify environment variables
echo $DOTNET_ROOT    # Should be: /home/youruser/.dotnet10
echo $PATH | grep powerplatform  # Should include .../powerplatform/tools

# 4. Test directly from tools directory
cd ~/powerplatform/tools && ./pac --version
```
