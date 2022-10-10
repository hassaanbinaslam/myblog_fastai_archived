---
keywords: fastai
description: This is a practice notebook for implementing linear regression in PyTorch.
title: Linear Regression with PyTorch
toc: true 
badges: true
comments: true
categories: [pytorch]
keyword: [ml, dl, linear, regression, pytorch]
image: images/copied_from_nb/images/2022-10-10-pytorch-linear-regression.jpeg
nb_path: _notebooks/2022-10-10-pytorch-linear-regression.ipynb
layout: notebook
---

<!--
#################################################
### THIS FILE WAS AUTOGENERATED! DO NOT EDIT! ###
#################################################
# file to edit: _notebooks/2022-10-10-pytorch-linear-regression.ipynb
-->

<div class="container" id="notebook-container">
        
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p><img src="/myblog/images/copied_from_nb/images/2022-10-10-pytorch-linear-regression.jpeg" alt=""></p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="Introduction">Introduction<a class="anchor-link" href="#Introduction"> </a></h1><p>In this notebook we will train a linear regression model using PyTorch.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="Environment">Environment<a class="anchor-link" href="#Environment"> </a></h1><p>This notebook is prepared with Google Colab.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="Credits">Credits<a class="anchor-link" href="#Credits"> </a></h1><p>This notebook takes inspiration from book "Deep Learning with PyTorch Step-by-Step" by "Daniel Voigt Godoy". Official GitHub repository for this book has a useful notebooks: <a href="https://github.com/dvgodoy/PyTorchStepByStep">https://github.com/dvgodoy/PyTorchStepByStep</a></p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="prepare-linear-data">prepare linear data<a class="anchor-link" href="#prepare-linear-data"> </a></h1><p>import numpy as np
np.random.seed(0)</p>
<p>"""
y = mx + c + e
"""</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="k">def</span> <span class="nf">generate_linear_data</span><span class="p">(</span><span class="n">n_data_points</span><span class="o">=</span><span class="mi">100</span><span class="p">,</span> <span class="n">true_m</span><span class="o">=</span><span class="mi">1</span><span class="p">,</span> <span class="n">true_b</span><span class="o">=</span><span class="mi">1</span><span class="p">):</span>
    <span class="n">x</span> <span class="o">=</span> <span class="n">np</span><span class="o">.</span><span class="n">random</span><span class="o">.</span><span class="n">rand</span><span class="p">(</span><span class="n">n_data_points</span><span class="p">,</span> <span class="mi">1</span><span class="p">)</span>
    <span class="n">y</span> <span class="o">=</span> <span class="n">true_m</span> <span class="o">*</span> <span class="n">x</span> <span class="o">+</span> <span class="n">true_b</span> <span class="o">+</span> <span class="p">(</span><span class="o">.</span><span class="mi">1</span> <span class="o">*</span> <span class="n">np</span><span class="o">.</span><span class="n">random</span><span class="o">.</span><span class="n">randn</span><span class="p">(</span><span class="n">n_data_points</span><span class="p">,</span> <span class="mi">1</span><span class="p">))</span>
    <span class="k">return</span> <span class="n">x</span><span class="p">,</span> <span class="n">y</span>


<span class="n">n_data_points</span> <span class="o">=</span> <span class="mi">100</span>
<span class="n">true_m</span> <span class="o">=</span> <span class="mi">2</span>
<span class="n">true_b</span> <span class="o">=</span> <span class="mi">1</span>
<span class="n">x</span><span class="p">,</span> <span class="n">y</span> <span class="o">=</span> <span class="n">generate_linear_data</span><span class="p">(</span><span class="n">n_data_points</span><span class="p">,</span> <span class="n">true_m</span><span class="p">,</span> <span class="n">true_b</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">matplotlib.pyplot</span> <span class="k">as</span> <span class="nn">plt</span>

<span class="n">fig</span><span class="p">,</span> <span class="n">ax</span> <span class="o">=</span> <span class="n">plt</span><span class="o">.</span><span class="n">subplots</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">1</span><span class="p">,</span> <span class="n">figsize</span><span class="o">=</span><span class="p">(</span><span class="mi">6</span><span class="p">,</span> <span class="mi">6</span><span class="p">))</span>
<span class="n">ax</span><span class="o">.</span><span class="n">scatter</span><span class="p">(</span><span class="n">x</span><span class="p">,</span> <span class="n">y</span><span class="p">)</span>
<span class="n">ax</span><span class="o">.</span><span class="n">set_xlabel</span><span class="p">(</span><span class="s1">&#39;x&#39;</span><span class="p">)</span>
<span class="n">ax</span><span class="o">.</span><span class="n">set_ylabel</span><span class="p">(</span><span class="s1">&#39;y&#39;</span><span class="p">)</span>
<span class="n">ax</span><span class="o">.</span><span class="n">set_title</span><span class="p">(</span><span class="s1">&#39;Generated Full Dataset&#39;</span><span class="p">)</span>
<span class="n">plt</span><span class="o">.</span><span class="n">show</span><span class="p">()</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">torch</span>

<span class="n">x_tensor</span> <span class="o">=</span> <span class="n">torch</span><span class="o">.</span><span class="n">as_tensor</span><span class="p">(</span><span class="n">x</span><span class="p">)</span><span class="o">.</span><span class="n">float</span><span class="p">()</span>
<span class="n">y_tensor</span> <span class="o">=</span> <span class="n">torch</span><span class="o">.</span><span class="n">as_tensor</span><span class="p">(</span><span class="n">y</span><span class="p">)</span><span class="o">.</span><span class="n">float</span><span class="p">()</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">torch.utils.data</span> <span class="kn">import</span> <span class="n">DataLoader</span><span class="p">,</span> <span class="n">TensorDataset</span><span class="p">,</span> <span class="n">random_split</span>

<span class="n">dataset</span> <span class="o">=</span> <span class="n">TensorDataset</span><span class="p">(</span><span class="n">x_tensor</span><span class="p">,</span> <span class="n">y_tensor</span><span class="p">)</span>

<span class="c1"># Performs the split</span>
<span class="n">ratio</span> <span class="o">=</span> <span class="o">.</span><span class="mi">8</span>
<span class="n">n_total</span> <span class="o">=</span> <span class="nb">len</span><span class="p">(</span><span class="n">dataset</span><span class="p">)</span>
<span class="n">n_train</span> <span class="o">=</span> <span class="nb">int</span><span class="p">(</span><span class="n">n_total</span> <span class="o">*</span> <span class="n">ratio</span><span class="p">)</span>
<span class="n">n_val</span> <span class="o">=</span> <span class="n">n_total</span> <span class="o">-</span> <span class="n">n_train</span>

<span class="n">train_data</span><span class="p">,</span> <span class="n">val_data</span> <span class="o">=</span> <span class="n">random_split</span><span class="p">(</span><span class="n">dataset</span><span class="p">,</span> <span class="p">[</span><span class="n">n_train</span><span class="p">,</span> <span class="n">n_val</span><span class="p">])</span>

<span class="c1"># Builds a loader of each set</span>
<span class="n">train_loader</span> <span class="o">=</span> <span class="n">DataLoader</span><span class="p">(</span>
    <span class="n">dataset</span><span class="o">=</span><span class="n">train_data</span><span class="p">,</span>
    <span class="n">batch_size</span><span class="o">=</span><span class="mi">16</span><span class="p">,</span>
    <span class="n">shuffle</span><span class="o">=</span><span class="kc">True</span>
<span class="p">)</span>
<span class="n">val_loader</span> <span class="o">=</span> <span class="n">DataLoader</span><span class="p">(</span><span class="n">dataset</span><span class="o">=</span><span class="n">val_data</span><span class="p">,</span> <span class="n">batch_size</span><span class="o">=</span><span class="mi">16</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># get data from dataset</span>
<span class="n">train_data_list</span> <span class="o">=</span> <span class="nb">list</span><span class="p">(</span><span class="nb">iter</span><span class="p">(</span><span class="n">train_data</span><span class="p">))</span>
<span class="n">val_data_list</span> <span class="o">=</span> <span class="nb">list</span><span class="p">(</span><span class="nb">iter</span><span class="p">(</span><span class="n">val_data</span><span class="p">))</span>

<span class="c1"># get x,y from dataset tuples list</span>
<span class="n">x_train</span> <span class="o">=</span> <span class="p">[</span><span class="n">e</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="o">.</span><span class="n">numpy</span><span class="p">()</span> <span class="k">for</span> <span class="n">e</span> <span class="ow">in</span> <span class="n">train_data_list</span><span class="p">]</span>
<span class="n">y_train</span> <span class="o">=</span> <span class="p">[</span><span class="n">e</span><span class="p">[</span><span class="mi">1</span><span class="p">]</span><span class="o">.</span><span class="n">numpy</span><span class="p">()</span> <span class="k">for</span> <span class="n">e</span> <span class="ow">in</span> <span class="n">train_data_list</span><span class="p">]</span>

<span class="n">x_val</span> <span class="o">=</span> <span class="p">[</span><span class="n">e</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="o">.</span><span class="n">numpy</span><span class="p">()</span> <span class="k">for</span> <span class="n">e</span> <span class="ow">in</span> <span class="n">val_data_list</span><span class="p">]</span>
<span class="n">y_val</span> <span class="o">=</span> <span class="p">[</span><span class="n">e</span><span class="p">[</span><span class="mi">1</span><span class="p">]</span><span class="o">.</span><span class="n">numpy</span><span class="p">()</span> <span class="k">for</span> <span class="n">e</span> <span class="ow">in</span> <span class="n">val_data_list</span><span class="p">]</span>

<span class="c1"># plot data</span>
<span class="n">figure</span><span class="p">,</span> <span class="n">axes</span> <span class="o">=</span> <span class="n">plt</span><span class="o">.</span><span class="n">subplots</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">3</span><span class="p">,</span> <span class="n">figsize</span><span class="o">=</span><span class="p">(</span><span class="mi">10</span><span class="p">,</span><span class="mi">5</span><span class="p">))</span>
<span class="n">figure</span><span class="o">.</span><span class="n">suptitle</span><span class="p">(</span><span class="s1">&#39;Train and Validation Dataset&#39;</span><span class="p">)</span>

<span class="n">axes</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="o">.</span><span class="n">set_title</span><span class="p">(</span><span class="s1">&#39;Training Data&#39;</span><span class="p">)</span>
<span class="n">axes</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="o">.</span><span class="n">scatter</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>

<span class="n">axes</span><span class="p">[</span><span class="mi">1</span><span class="p">]</span><span class="o">.</span><span class="n">set_title</span><span class="p">(</span><span class="s1">&#39;Validation Data&#39;</span><span class="p">)</span>
<span class="n">axes</span><span class="p">[</span><span class="mi">1</span><span class="p">]</span><span class="o">.</span><span class="n">scatter</span><span class="p">(</span><span class="n">x_val</span><span class="p">,</span> <span class="n">y_val</span><span class="p">)</span>

<span class="n">axes</span><span class="p">[</span><span class="mi">2</span><span class="p">]</span><span class="o">.</span><span class="n">set_title</span><span class="p">(</span><span class="s1">&#39;Combined Data&#39;</span><span class="p">)</span>
<span class="n">axes</span><span class="p">[</span><span class="mi">2</span><span class="p">]</span><span class="o">.</span><span class="n">scatter</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>
<span class="n">axes</span><span class="p">[</span><span class="mi">2</span><span class="p">]</span><span class="o">.</span><span class="n">scatter</span><span class="p">(</span><span class="n">x_val</span><span class="p">,</span> <span class="n">y_val</span><span class="p">)</span>
<span class="n">plt</span><span class="o">.</span><span class="n">show</span><span class="p">()</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">torch.nn</span> <span class="k">as</span> <span class="nn">nn</span>
<span class="kn">import</span> <span class="nn">torch.optim</span> <span class="k">as</span> <span class="nn">optim</span>
<span class="kn">from</span> <span class="nn">torch.utils.tensorboard</span> <span class="kn">import</span> <span class="n">SummaryWriter</span>

<span class="n">device</span> <span class="o">=</span> <span class="s1">&#39;cuda&#39;</span> <span class="k">if</span> <span class="n">torch</span><span class="o">.</span><span class="n">cuda</span><span class="o">.</span><span class="n">is_available</span><span class="p">()</span> <span class="k">else</span> <span class="s1">&#39;cpu&#39;</span>

<span class="c1"># Sets learning rate - this is &quot;eta&quot; ~ the &quot;n&quot; like Greek letter</span>
<span class="n">lr</span> <span class="o">=</span> <span class="mf">0.1</span>

<span class="n">torch</span><span class="o">.</span><span class="n">manual_seed</span><span class="p">(</span><span class="mi">0</span><span class="p">)</span>
<span class="c1"># Now we can create a model and send it at once to the device</span>
<span class="n">model</span> <span class="o">=</span> <span class="n">nn</span><span class="o">.</span><span class="n">Linear</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">1</span><span class="p">)</span><span class="o">.</span><span class="n">to</span><span class="p">(</span><span class="n">device</span><span class="p">)</span>

<span class="c1"># Defines a SGD optimizer to update the parameters (now retrieved directly from the model)</span>
<span class="n">optimizer</span> <span class="o">=</span> <span class="n">optim</span><span class="o">.</span><span class="n">SGD</span><span class="p">(</span><span class="n">model</span><span class="o">.</span><span class="n">parameters</span><span class="p">(),</span> <span class="n">lr</span><span class="o">=</span><span class="n">lr</span><span class="p">)</span>

<span class="c1"># Defines a MSE loss function</span>
<span class="n">loss_fn</span> <span class="o">=</span> <span class="n">nn</span><span class="o">.</span><span class="n">MSELoss</span><span class="p">(</span><span class="n">reduction</span><span class="o">=</span><span class="s1">&#39;mean&#39;</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="nb">print</span><span class="p">(</span><span class="n">model</span><span class="o">.</span><span class="n">state_dict</span><span class="p">())</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="c1">## helper function for training</span>
<span class="k">def</span> <span class="nf">make_train_step_fn</span><span class="p">(</span><span class="n">model</span><span class="p">,</span> <span class="n">loss_fn</span><span class="p">,</span> <span class="n">optimizer</span><span class="p">):</span>
    <span class="c1"># Builds function that performs a step in the train loop</span>
    <span class="k">def</span> <span class="nf">perform_train_step_fn</span><span class="p">(</span><span class="n">x</span><span class="p">,</span> <span class="n">y</span><span class="p">):</span>
        <span class="c1"># Sets model to TRAIN mode</span>
        <span class="n">model</span><span class="o">.</span><span class="n">train</span><span class="p">()</span>
        
        <span class="c1"># Step 1 - Computes our model&#39;s predicted output - forward pass</span>
        <span class="n">yhat</span> <span class="o">=</span> <span class="n">model</span><span class="p">(</span><span class="n">x</span><span class="p">)</span>
        <span class="c1"># Step 2 - Computes the loss</span>
        <span class="n">loss</span> <span class="o">=</span> <span class="n">loss_fn</span><span class="p">(</span><span class="n">yhat</span><span class="p">,</span> <span class="n">y</span><span class="p">)</span>
        <span class="c1"># Step 3 - Computes gradients for both &quot;a&quot; and &quot;b&quot; parameters</span>
        <span class="n">loss</span><span class="o">.</span><span class="n">backward</span><span class="p">()</span>
        <span class="c1"># Step 4 - Updates parameters using gradients and the learning rate</span>
        <span class="n">optimizer</span><span class="o">.</span><span class="n">step</span><span class="p">()</span>
        <span class="n">optimizer</span><span class="o">.</span><span class="n">zero_grad</span><span class="p">()</span>
        
        <span class="c1"># Returns the loss</span>
        <span class="k">return</span> <span class="n">loss</span><span class="o">.</span><span class="n">item</span><span class="p">()</span>
    
    <span class="c1"># Returns the function that will be called inside the train loop</span>
    <span class="k">return</span> <span class="n">perform_train_step_fn</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="k">def</span> <span class="nf">make_val_step_fn</span><span class="p">(</span><span class="n">model</span><span class="p">,</span> <span class="n">loss_fn</span><span class="p">):</span>
    <span class="c1"># Builds function that performs a step in the validation loop</span>
    <span class="k">def</span> <span class="nf">perform_val_step_fn</span><span class="p">(</span><span class="n">x</span><span class="p">,</span> <span class="n">y</span><span class="p">):</span>
        <span class="c1"># Sets model to EVAL mode</span>
        <span class="n">model</span><span class="o">.</span><span class="n">eval</span><span class="p">()</span>
        
        <span class="c1"># Step 1 - Computes our model&#39;s predicted output - forward pass</span>
        <span class="n">yhat</span> <span class="o">=</span> <span class="n">model</span><span class="p">(</span><span class="n">x</span><span class="p">)</span>
        <span class="c1"># Step 2 - Computes the loss</span>
        <span class="n">loss</span> <span class="o">=</span> <span class="n">loss_fn</span><span class="p">(</span><span class="n">yhat</span><span class="p">,</span> <span class="n">y</span><span class="p">)</span>
        <span class="c1"># There is no need to compute Steps 3 and 4, since we don&#39;t update parameters during evaluation</span>
        <span class="k">return</span> <span class="n">loss</span><span class="o">.</span><span class="n">item</span><span class="p">()</span>
    
    <span class="k">return</span> <span class="n">perform_val_step_fn</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">datetime</span>

<span class="c1"># Creates the train_step function for our model, loss function and optimizer</span>
<span class="n">train_step_fn</span> <span class="o">=</span> <span class="n">make_train_step_fn</span><span class="p">(</span><span class="n">model</span><span class="p">,</span> <span class="n">loss_fn</span><span class="p">,</span> <span class="n">optimizer</span><span class="p">)</span>

<span class="c1"># Creates the val_step function for our model and loss function</span>
<span class="n">val_step_fn</span> <span class="o">=</span> <span class="n">make_val_step_fn</span><span class="p">(</span><span class="n">model</span><span class="p">,</span> <span class="n">loss_fn</span><span class="p">)</span>

<span class="c1"># Creates a Summary Writer to interface with TensorBoard</span>
<span class="n">timestamp</span> <span class="o">=</span> <span class="n">datetime</span><span class="o">.</span><span class="n">datetime</span><span class="o">.</span><span class="n">utcnow</span><span class="p">()</span><span class="o">.</span><span class="n">strftime</span><span class="p">(</span><span class="s1">&#39;%Y-%m-</span><span class="si">%d</span><span class="s1">-%H.%M.%S&#39;</span><span class="p">)</span>
<span class="n">writer</span> <span class="o">=</span> <span class="n">SummaryWriter</span><span class="p">(</span><span class="sa">f</span><span class="s1">&#39;runs/simple_linear_regression/</span><span class="si">{</span><span class="n">timestamp</span><span class="si">}</span><span class="s1">&#39;</span><span class="p">)</span>

<span class="c1"># Fetches a single mini-batch so we can use add_graph</span>
<span class="n">x_sample</span><span class="p">,</span> <span class="n">y_sample</span> <span class="o">=</span> <span class="nb">next</span><span class="p">(</span><span class="nb">iter</span><span class="p">(</span><span class="n">train_loader</span><span class="p">))</span>
<span class="n">writer</span><span class="o">.</span><span class="n">add_graph</span><span class="p">(</span><span class="n">model</span><span class="p">,</span> <span class="n">x_sample</span><span class="o">.</span><span class="n">to</span><span class="p">(</span><span class="n">device</span><span class="p">))</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="k">def</span> <span class="nf">mini_batch</span><span class="p">(</span><span class="n">device</span><span class="p">,</span> <span class="n">data_loader</span><span class="p">,</span> <span class="n">step_fn</span><span class="p">):</span>
    <span class="n">mini_batch_losses</span> <span class="o">=</span> <span class="p">[]</span>
    <span class="k">for</span> <span class="n">x_batch</span><span class="p">,</span> <span class="n">y_batch</span> <span class="ow">in</span> <span class="n">data_loader</span><span class="p">:</span>
        <span class="n">x_batch</span> <span class="o">=</span> <span class="n">x_batch</span><span class="o">.</span><span class="n">to</span><span class="p">(</span><span class="n">device</span><span class="p">)</span>
        <span class="n">y_batch</span> <span class="o">=</span> <span class="n">y_batch</span><span class="o">.</span><span class="n">to</span><span class="p">(</span><span class="n">device</span><span class="p">)</span>

        <span class="n">mini_batch_loss</span> <span class="o">=</span> <span class="n">step_fn</span><span class="p">(</span><span class="n">x_batch</span><span class="p">,</span> <span class="n">y_batch</span><span class="p">)</span>
        <span class="n">mini_batch_losses</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">mini_batch_loss</span><span class="p">)</span>

    <span class="n">loss</span> <span class="o">=</span> <span class="n">np</span><span class="o">.</span><span class="n">mean</span><span class="p">(</span><span class="n">mini_batch_losses</span><span class="p">)</span>
    <span class="k">return</span> <span class="n">loss</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="n">n_epochs</span> <span class="o">=</span> <span class="mi">200</span>

<span class="n">losses</span> <span class="o">=</span> <span class="p">[]</span>
<span class="n">val_losses</span> <span class="o">=</span> <span class="p">[]</span>

<span class="k">for</span> <span class="n">epoch</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="n">n_epochs</span><span class="p">):</span>
    <span class="c1"># inner loop</span>
    <span class="n">loss</span> <span class="o">=</span> <span class="n">mini_batch</span><span class="p">(</span><span class="n">device</span><span class="p">,</span> <span class="n">train_loader</span><span class="p">,</span> <span class="n">train_step_fn</span><span class="p">)</span>
    <span class="n">losses</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">loss</span><span class="p">)</span>
    
    <span class="c1"># VALIDATION</span>
    <span class="c1"># no gradients in validation!</span>
    <span class="k">with</span> <span class="n">torch</span><span class="o">.</span><span class="n">no_grad</span><span class="p">():</span>
        <span class="n">val_loss</span> <span class="o">=</span> <span class="n">mini_batch</span><span class="p">(</span><span class="n">device</span><span class="p">,</span> <span class="n">val_loader</span><span class="p">,</span> <span class="n">val_step_fn</span><span class="p">)</span>
        <span class="n">val_losses</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">val_loss</span><span class="p">)</span>
    
    <span class="c1"># Records both losses for each epoch under the main tag &quot;loss&quot;</span>
    <span class="n">writer</span><span class="o">.</span><span class="n">add_scalars</span><span class="p">(</span><span class="n">main_tag</span><span class="o">=</span><span class="s1">&#39;loss&#39;</span><span class="p">,</span>
                       <span class="n">tag_scalar_dict</span><span class="o">=</span><span class="p">{</span><span class="s1">&#39;training&#39;</span><span class="p">:</span> <span class="n">loss</span><span class="p">,</span> <span class="s1">&#39;validation&#39;</span><span class="p">:</span> <span class="n">val_loss</span><span class="p">},</span>
                       <span class="n">global_step</span><span class="o">=</span><span class="n">epoch</span><span class="p">)</span>
    
    <span class="nb">print</span><span class="p">(</span><span class="sa">f</span><span class="s2">&quot;epoch: </span><span class="si">{</span><span class="n">epoch</span><span class="si">:</span><span class="s2">3</span><span class="si">}</span><span class="s2">, train loss: </span><span class="si">{</span><span class="n">loss</span><span class="si">:</span><span class="s2">.5f</span><span class="si">}</span><span class="s2">, valid loss: </span><span class="si">{</span><span class="n">val_loss</span><span class="si">:</span><span class="s2">.5f</span><span class="si">}</span><span class="s2">&quot;</span><span class="p">)</span>

<span class="c1"># Closes the writer</span>
<span class="n">writer</span><span class="o">.</span><span class="n">close</span><span class="p">()</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="nb">print</span><span class="p">(</span><span class="n">model</span><span class="o">.</span><span class="n">state_dict</span><span class="p">())</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="sa">f</span><span class="s1">&#39;runs/simple_linear_regression/</span><span class="si">{</span><span class="n">timestamp</span><span class="si">}</span><span class="s1">&#39;</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="o">%</span><span class="k">load_ext</span> tensorboard
<span class="o">%</span><span class="k">tensorboard</span> --logdir runs
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">sklearn.linear_model</span> <span class="kn">import</span> <span class="n">LinearRegression</span>

<span class="n">reg</span> <span class="o">=</span> <span class="n">LinearRegression</span><span class="p">()</span><span class="o">.</span><span class="n">fit</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="n">reg</span><span class="o">.</span><span class="n">coef_</span><span class="p">,</span> <span class="n">reg</span><span class="o">.</span><span class="n">intercept_</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

</div>
 
