---
title: Deploying to Heroku manual
author: Victor Waichigo
tags: 'Heroku, Deployment'

---

<h1 id="a-guide-for-deploying-to-heroku">A guide for deploying to Heroku</h1>
<h2 id="info">Info:</h2>
<p>This guide shows a simple step by step tutorial for deploying django applications.</p>
<blockquote>
<p>Tested with django 2.2.5</p>
</blockquote>
<h2 id="requirements">Requirements:</h2>
<h4 id="starters">Starters:</h4>
<ul>
<li>A heroku account</li>
<li>Heroku CLI</li>
</ul>
<h4 id="files-needed">Files needed:</h4>
<ul>
<li>A django application</li>
<li>A Procfile</li>
<li>a .env file</li>
<li>a runtime.txt file</li>
</ul>
<h4 id="installations">Installations:</h4>
<ul>
<li>gunicorn</li>
<li>whitenoise</li>
<li>Python dotenv</li>
<li>Python-decouple</li>
<li>dj-database-url</li>
<li>psycopg2</li>
</ul>
<h1 id="lets-setup-our-app-">Let’s setup our app :)</h1>
<h4 id="login-to-your-heroku-account">1. Login to your heroku account</h4>
<pre class=" language-python"><code class="prism  language-python">	 heroku login
</code></pre>
<h4 id="create-a-heroku-application">2. Create a heroku application</h4>
<p>In this case, we’ll create an app called 	<code>picsgarage</code></p>
<pre class=" language-python"><code class="prism  language-python">	heroku create picsgarage
</code></pre>
<p>Heroku apps include a <code>Procfile</code> that specifies the commands that are executed by the app’s dynos.</p>
<h4 id="create-a-procfile">3. Create a Procfile</h4>
<pre class=" language-python"><code class="prism  language-python">$ touch Procfile
</code></pre>
<p>Add the following settings to your Procfile</p>
<pre class=" language-python"><code class="prism  language-python">web<span class="token punctuation">:</span> gunicorn your_project_name<span class="token punctuation">.</span>wsgi <span class="token operator">-</span><span class="token operator">-</span>log<span class="token operator">-</span><span class="token builtin">file</span> <span class="token operator">-</span>
</code></pre>
<h4 id="selecting-a-runtime">4. Selecting a runtime</h4>
<p>To specify a Python runtime, add a <code>runtime.txt</code> file to your app’s root 	    directory that declares the exact version number to use:</p>
<pre class=" language-python"><code class="prism  language-python">nano runtime<span class="token punctuation">.</span>txt
</code></pre>
<p>Add the version below to the runtime.txt file<br>
<code>python 	python-3.7.2</code></p>
<h4 id="whitenoise-django-static-files-settings">5. Whitenoise: Django Static Files settings</h4>
<p>First configure static related parameter in <code>settings.py</code></p>
<pre class=" language-python"><code class="prism  language-python">BASE_DIR <span class="token operator">=</span> os<span class="token punctuation">.</span>path<span class="token punctuation">.</span>dirname<span class="token punctuation">(</span>os<span class="token punctuation">.</span>path<span class="token punctuation">.</span>dirname<span class="token punctuation">(</span>os<span class="token punctuation">.</span>path<span class="token punctuation">.</span>abspath<span class="token punctuation">(</span>__file__<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token comment"># Static files (CSS, JavaScript, Images)</span>
<span class="token comment"># https://docs.djangoproject.com/en/1.9/howto/static-files/</span>
STATIC_ROOT <span class="token operator">=</span> os<span class="token punctuation">.</span>path<span class="token punctuation">.</span>join<span class="token punctuation">(</span>BASE_DIR<span class="token punctuation">,</span> <span class="token string">'staticfiles'</span><span class="token punctuation">)</span>
STATIC_URL <span class="token operator">=</span> <span class="token string">'/static/'</span>

<span class="token comment"># Extra places for collectstatic to find static files.</span>
STATICFILES_DIRS <span class="token operator">=</span> <span class="token punctuation">(</span>
    os<span class="token punctuation">.</span>path<span class="token punctuation">.</span>join<span class="token punctuation">(</span>BASE_DIR<span class="token punctuation">,</span> <span class="token string">'static'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
<span class="token punctuation">)</span>
</code></pre>
<p>Django does not support serving static files in production. However, WhiteNoise project can integrate into your Django application, and was designed with exactly this purpose in mind.<br>
So, let’s install <code>whitenoise</code></p>
<pre class=" language-python"><code class="prism  language-python">pip install whitenoise
</code></pre>
<p>Next, install <code>WhiteNoise</code> into your Django application. This is done in <code>settings.py’s middleware section</code> (at the top):</p>
<pre class=" language-python"><code class="prism  language-python">MIDDLEWARE_CLASSES <span class="token operator">=</span> <span class="token punctuation">(</span>
    <span class="token comment"># Simplified static file serving.</span>
    <span class="token comment"># https://warehouse.python.org/project/whitenoise/</span>
    <span class="token string">'whitenoise.middleware.WhiteNoiseMiddleware'</span><span class="token punctuation">,</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>```

Add the following setting to `settings<span class="token punctuation">.</span>py` <span class="token keyword">in</span> the static files section to enable gzip functionality<span class="token punctuation">.</span>

```python
<span class="token comment"># Simplified static file serving.</span>
<span class="token comment"># https://warehouse.python.org/project/whitenoise/</span>

STATICFILES_STORAGE <span class="token operator">=</span> <span class="token string">'whitenoise.storage.CompressedManifestStaticFilesStorage'</span>
</code></pre>
<h4 id="requirements-file.">Requirements file.</h4>
<p>If your under a virtual environment run the command below to generate the requirements.txt file which heroku will use to install python package dependencies.</p>
<pre class=" language-python"><code class="prism  language-python">pip freeze <span class="token operator">&gt;</span> requirements<span class="token punctuation">.</span>txt
</code></pre>
<p>Make sure the following are on the file</p>
<pre><code>dj-database-url==0.4.2
Django==2.0.2
dj-static==0.0.6
gunicorn==19.7.1
psycopg2-binary==2.7.4
python-decouple==3.1
unicodecsv==0.14.1
whitenoise==3.3.1
Unipath==1.0
</code></pre>
<h4 id="adding-environment-variables-to-.env-file">Adding environment variables to .env file</h4>
<p>Create a <code>.env</code> file on your root folder by running the command,</p>
<pre class=" language-python"><code class="prism  language-python">touch <span class="token punctuation">.</span>env 
</code></pre>
<p>Add the following to the <code>.env</code> file</p>
<pre class=" language-python"><code class="prism  language-python">SECRET_KEY<span class="token operator">=</span><span class="token string">'309899das98yaifuwh32547lmnj3k5lb34hk6bhj5b36i'</span>  
DEBUG<span class="token operator">=</span><span class="token boolean">True</span>  
DB_NAME<span class="token operator">=</span><span class="token string">'picsgarage'</span>  
DB_USER<span class="token operator">=</span><span class="token string">'vick'</span>  
DB_PASSWORD<span class="token operator">=</span><span class="token string">'p'</span>  
DB_HOST<span class="token operator">=</span><span class="token string">'127.0.0.1'</span>  
MODE<span class="token operator">=</span><span class="token string">'dev'</span>  
ALLOWED_HOSTS<span class="token operator">=</span><span class="token string">'*'</span>
</code></pre>
<p>or an easier way</p>
<pre class=" language-python"><code class="prism  language-python">SECRET_KEY<span class="token operator">=</span><span class="token string">'309899das98yaifuwh32547lmnj3k5lb34hk6bhj5b36i'</span>  
DEBUG<span class="token operator">=</span><span class="token boolean">True</span> 
DATABASE_URL<span class="token operator">=</span><span class="token string">'postgres://vick:p@localhost/picsgarage'</span>
MODE<span class="token operator">=</span><span class="token string">'dev'</span>  
ALLOWED_HOSTS<span class="token operator">=</span><span class="token string">'*'</span>
</code></pre>
<h4 id="install-the-following-python-packages-first">Install the following python packages first;</h4>
<pre class=" language-python"><code class="prism  language-python">pip install python<span class="token operator">-</span>dotenv
</code></pre>
<pre class=" language-python"><code class="prism  language-python">pip install django<span class="token operator">-</span>heroku
</code></pre>
<pre class=" language-python"><code class="prism  language-python">pip install python<span class="token operator">-</span>decouple
</code></pre>
<pre class=" language-python"><code class="prism  language-python">pip install dj<span class="token operator">-</span>database<span class="token operator">-</span>url
</code></pre>
<h4 id="add-the-following-to-the-settings.py-imports">Add the following to the <code>settings.py</code> imports</h4>
<pre class=" language-python"><code class="prism  language-python"><span class="token keyword">import</span> os  
  
<span class="token keyword">import</span> dj_database_url  
<span class="token keyword">import</span> django_heroku  
<span class="token keyword">from</span> decouple <span class="token keyword">import</span> config<span class="token punctuation">,</span> Csv  
<span class="token keyword">from</span> dotenv <span class="token keyword">import</span> load_dotenv  
  
load_dotenv<span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<h4 id="adding-the-secret_ket-to-the-config-file">Adding the <code>SECRET_KET</code> to the config file</h4>
<pre class=" language-python"><code class="prism  language-python">SECRET_KEY <span class="token operator">=</span> config<span class="token punctuation">(</span><span class="token string">'SECRET_KEY'</span><span class="token punctuation">)</span>
</code></pre>
<h4 id="setting-debug-values">Setting <code>DEBUG</code> values</h4>
<pre class=" language-python"><code class="prism  language-python">DEBUG <span class="token operator">=</span> config<span class="token punctuation">(</span><span class="token string">'DEBUG'</span><span class="token punctuation">,</span> default<span class="token operator">=</span><span class="token boolean">False</span><span class="token punctuation">,</span> cast<span class="token operator">=</span><span class="token builtin">bool</span><span class="token punctuation">)</span>
</code></pre>
<h4 id="databases-configurations"><code>DATABASES</code> configurations</h4>
<pre class=" language-python"><code class="prism  language-python">DATABASES <span class="token operator">=</span> <span class="token punctuation">{</span>  
  <span class="token string">'default'</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>  
  <span class="token string">'ENGINE'</span><span class="token punctuation">:</span> <span class="token string">'django.db.backends.postgresql_psycopg2'</span><span class="token punctuation">,</span>  
  <span class="token string">'NAME'</span><span class="token punctuation">:</span> config<span class="token punctuation">(</span><span class="token string">'DB_NAME'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>  
  <span class="token string">'USER'</span><span class="token punctuation">:</span> config<span class="token punctuation">(</span><span class="token string">'DB_USER'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>  
  <span class="token string">'PASSWORD'</span><span class="token punctuation">:</span> config<span class="token punctuation">(</span><span class="token string">'DB_PASSWORD'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>  
  <span class="token string">'HOST'</span><span class="token punctuation">:</span> config<span class="token punctuation">(</span><span class="token string">'DB_HOST'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>  
  <span class="token punctuation">}</span>  
<span class="token punctuation">}</span>
</code></pre>
<p>or use the easy way</p>
<pre class=" language-python"><code class="prism  language-python">DATABASES <span class="token operator">=</span> <span class="token punctuation">{</span>  
    <span class="token string">'default'</span><span class="token punctuation">:</span>  dj_database_url<span class="token punctuation">.</span>config<span class="token punctuation">(</span>  
        default<span class="token operator">=</span>config<span class="token punctuation">(</span><span class="token string">'DATABASE_URL'</span><span class="token punctuation">)</span>  
    <span class="token punctuation">)</span>  
<span class="token punctuation">}</span>
</code></pre>
<p>Then add the code below under the <code>DATABASES</code> object</p>
<pre class=" language-python"><code class="prism  language-python">db_env <span class="token operator">=</span> dj_database_url<span class="token punctuation">.</span>config<span class="token punctuation">(</span>conn_max_age<span class="token operator">=</span><span class="token number">500</span><span class="token punctuation">)</span>  
DATABASES<span class="token punctuation">[</span><span class="token string">'default'</span><span class="token punctuation">]</span><span class="token punctuation">.</span>update<span class="token punctuation">(</span>db_env<span class="token punctuation">)</span>  
ALLOWED_HOSTS <span class="token operator">=</span> config<span class="token punctuation">(</span><span class="token string">'ALLOWED_HOSTS'</span><span class="token punctuation">,</span> cast<span class="token operator">=</span>Csv<span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
</code></pre>
<h4 id="pushing-config-variables-to-heroku">Pushing config variables to heroku</h4>
<pre class=" language-python"><code class="prism  language-python">heroku config<span class="token punctuation">:</span><span class="token builtin">set</span> $<span class="token punctuation">(</span>cat <span class="token punctuation">.</span>env <span class="token operator">|</span> sed <span class="token string">'/^$/d; /#[[:print:]]*$/d'</span><span class="token punctuation">)</span>
</code></pre>
<h4 id="pushing-your-db-to-heroku">Pushing your DB to heroku</h4>
<p>Run the command below to push your local db to heroku</p>
<pre><code>syntax ==&gt; heroku pg:push &lt;db_name&gt; DATABASE_URL --app &lt;heroku_app_name&gt;
</code></pre>
<pre class=" language-python"><code class="prism  language-python">heroku pg<span class="token punctuation">:</span>push picsgarage DATABASE_URL <span class="token operator">-</span><span class="token operator">-</span>app picsgarage
</code></pre>
<h3 id="and-thats-it...your-app-should-now-be-deployed-to-heroku">And that’s it…Your app should now be deployed to Heroku</h3>
<p>improved from @jakhax deployment manual</p>
<blockquote>
<p>© Victor Waichigo 2019</p>
</blockquote>

