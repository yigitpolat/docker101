# Step 1: Create a python app \(without using Docker\)

Run the following command to create a file named `app.py` with a simple python program. \(copy-paste the entire code block\)

```bash
mkdir app && cd app
echo 'from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "hello world!"

if __name__ == "__main__":
    app.run(host="0.0.0.0")' > app.py
```

This is a simple python app that uses flask to expose a http web server on port 5000 \(5000 is the default port for flask\). Don't worry if you are not too familiar with python or flask, these concepts can be applied to an application written in any language.

## Optional Step for Local Run

**Optional:** If you have python and pip installed, you can run this app locally. If not, move on to the next step.

{% page-ref page="lab2.3-step-2-create-and-build-the-docker-image.md" %}

```bash
python3 --version
# Python 3.6.1
```

```bash
pip3 --version
# pip 9.0.1 from /usr/local/lib/python3.6/site-packages (python 3.6)
```

```bash
pip3 install flask
```

```bash
python3 app.py
# * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

