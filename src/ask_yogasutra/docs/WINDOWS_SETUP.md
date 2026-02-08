# Windows Setup Guide - Ask Yogasutra

A complete step-by-step guide to set up and run the Ask Yogasutra application on Windows.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Git** - [Download Git for Windows](https://git-scm.com/download/win)
- **Python 3.11** - The application requires Python 3.11 (NOT 3.14)
- **uv** - Fast Python package installer [Install uv](https://docs.astral.sh/uv/getting-started/installation/)

> **Important**: Do NOT use Python 3.14. Use Python 3.11 for compatibility with all required packages.

## Step 1: Install uv Package Manager

Open PowerShell and run:

```powershell
# Install uv using the official installer
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Restart your PowerShell after installation.

## Step 2: Install Python 3.11

Using uv, install Python 3.11:

```powershell
# Install Python 3.11 via uv
uv python install 3.11
```

Verify the installation:

```powershell
# List installed Python versions
py list
```

You should see Python 3.11 in the list.

## Step 3: Clone the Repository

```powershell
# Navigate to where you want to store the project
cd C:\Users\YourUsername\source\repos

# Clone the repository
git clone https://github.com/yourusername/Sarvadnya-Fork.git

# Navigate to the project directory
cd Sarvadnya-Fork\src\ask_yogasutra
```

## Step 4: Create Virtual Environment

Create a new virtual environment using Python 3.11:

```powershell
# Create virtual environment with Python 3.11
uv venv .venv --python 3.11
```

You should see output confirming the creation:
```
Using CPython 3.11.14
Creating virtual environment at: .venv
Activate with: .venv\Scripts\activate
```

## Step 5: Activate Virtual Environment

```powershell
# Activate the virtual environment
.venv\Scripts\Activate.ps1
```

Your prompt should now show `(.venv)` at the beginning.

> **Note**: If you get an execution policy error, run:
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```

## Step 6: Install Dependencies

Install all required packages using uv:

```powershell
# Install all dependencies from requirements.txt
uv pip install -r requirements.txt
```

This will install 71 packages including:
- streamlit
- llama-cpp-python
- sentence-transformers
- transformers
- llama-index
- pyarrow
- and more...

⏱️ **Installation time**: Approximately 2-5 minutes depending on your internet speed.

## Step 7: Fix PyTorch Version (IMPORTANT!)

The default PyTorch version (2.10.0) has DLL compatibility issues on Windows. Downgrade to version 2.4.0:

```powershell
# Uninstall the problematic version
uv pip uninstall torch

# Install the working version
uv pip install torch==2.4.0
```

> **Why?** PyTorch 2.10.0+ has DLL initialization errors on Windows (`WinError 1114: c10.dll failed`). Version 2.4.0 is stable and works properly.

## Step 8: Set Up Groq API Key

1. **Create a Groq Account**:
   - Go to https://console.groq.com/
   - Sign up for a free account
   - Navigate to API Keys section
   - Generate a new API key

2. **Set the Environment Variable**:

   Option A - Temporary (current session only):
   ```powershell
   $env:GROQ_API_KEY = "your-api-key-here"
   ```

   Option B - Permanent (recommended):
   ```powershell
   # Add to your PowerShell profile
   notepad $PROFILE
   
   # Add this line to the file:
   $env:GROQ_API_KEY = "your-api-key-here"
   
   # Save and close
   # Reload profile
   . $PROFILE
   ```

3. **Verify API Key is Set**:
   ```powershell
   python -c "import os; print('✓ API Key is set!' if os.getenv('GROQ_API_KEY') else '✗ API Key not found')"
   ```

## Step 9: Verify Installation

Run verification tests:

```powershell
# Test Python version
.venv\Scripts\python.exe --version
# Should show: Python 3.11.14

# Test PyTorch import
.venv\Scripts\python.exe -c "import torch; print(f'✓ PyTorch {torch.__version__}')"
# Should show: ✓ PyTorch 2.4.0

# Test key packages
.venv\Scripts\python.exe -c "import streamlit, llama_cpp, sentence_transformers; print('✓ All packages loaded!')"
# Should show: ✓ All packages loaded!
```

## Step 10: Run the Application

You have three different apps to choose from:

### Option A: GraphRAG Chatbot (Recommended)
```powershell
streamlit run graphrag/streamlit_main_graphrag.py
```

### Option B: LinearRAG Chatbot
```powershell
streamlit run linearrag/streamlit_main_linearrag.py
```

### Option C: Graph Visualization
```powershell
streamlit run utils/streamlit_main_visualization.py
```

The app will start and display:
```
  You can now view your Streamlit app in your browser.

  Local URL: http://localhost:8501
  Network URL: http://192.168.1.x:8501
```

## Step 11: Access the Application

Open your web browser and navigate to:
- **Local access**: http://localhost:8501
- **Network access**: Use the Network URL shown in the terminal

## Troubleshooting

### Issue 1: PyTorch DLL Error
**Error**: `OSError: [WinError 1114] DLL initialization failed`

**Solution**:
```powershell
uv pip uninstall torch
uv pip install torch==2.4.0
```

### Issue 2: Execution Policy Error
**Error**: `cannot be loaded because running scripts is disabled`

**Solution**:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### Issue 3: Python 3.14 Compatibility Issues
**Error**: Package installation failures

**Solution**: Use Python 3.11 instead:
```powershell
# Remove the .venv directory
Remove-Item -Recurse -Force .venv

# Recreate with Python 3.11
uv venv .venv --python 3.11

# Reinstall packages
uv pip install -r requirements.txt
uv pip install torch==2.4.0
```

### Issue 4: Virtual Environment Won't Activate
**Error**: `Activate.ps1 cannot be loaded`

**Solution**:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### Issue 5: Missing GROQ_API_KEY
**Error**: API key not found

**Solution**:
```powershell
# Set it temporarily
$env:GROQ_API_KEY = "your-api-key-here"

# Or add permanently to profile
notepad $PROFILE
# Add: $env:GROQ_API_KEY = "your-api-key-here"
```

### Issue 6: Port Already in Use
**Error**: `Address already in use`

**Solution**:
```powershell
# Run on a different port
streamlit run graphrag/streamlit_main_graphrag.py --server.port 8502
```

## Updating the Application

To update to the latest version:

```powershell
# Pull latest changes
git pull origin main

# With virtual environment activated
uv pip install -r requirements.txt --upgrade

# Ensure PyTorch stays at 2.4.0
uv pip install torch==2.4.0
```

## Uninstalling

To completely remove the application:

```powershell
# Deactivate virtual environment
deactivate

# Navigate out of the project directory
cd ..

# Remove the entire project folder
Remove-Item -Recurse -Force ask_yogasutra
```

## Quick Reference

### Common Commands

```powershell
# Activate virtual environment
.venv\Scripts\Activate.ps1

# Deactivate virtual environment
deactivate

# Run GraphRAG app
streamlit run graphrag/streamlit_main_graphrag.py

# Run LinearRAG app
streamlit run linearrag/streamlit_main_linearrag.py

# Run visualization
streamlit run utils/streamlit_main_visualization.py

# Stop the app
# Press Ctrl+C in the terminal
```

### Package Versions (Tested & Working)

- Python: **3.11.14**
- PyTorch: **2.4.0** (Important!)
- Streamlit: **1.37.0**
- llama-cpp-python: **0.2.72**
- sentence-transformers: **3.0.1**
- transformers: **4.53.0**

## Additional Resources

- **Main README**: See `README.md` for feature details
- **Groq API Docs**: https://console.groq.com/docs
- **uv Documentation**: https://docs.astral.sh/uv/
- **Streamlit Docs**: https://docs.streamlit.io/

## Getting Help

If you encounter issues:

1. Check this troubleshooting section
2. Review the error messages carefully
3. Verify Python version is 3.11
4. Ensure PyTorch is version 2.4.0
5. Confirm GROQ_API_KEY is set correctly

---

**Last Updated**: February 2026  
**Tested On**: Windows 10/11, Python 3.11.14, PyTorch 2.4.0
