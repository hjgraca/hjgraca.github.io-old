---
layout:     post
title:      "Unit testing Asp.Net Mvc Html Helper"
subtitle:   "Mocked wrapper for ASP.Net MVC Html Helper"
date:       2014-10-31 10:52:00
author:     "Henrique Graca"
header-img: "img/fence.jpg"
---
<p>
	While working on an MVC project and using TDD I had to create an HtmlHelper extension method, mocking and testing turned out to be a very long and bumpy road. In this blog post I'll show you a way to mock HtmlHelper.
</p>
<h2 class="section-heading">The Html Extension method</h2>

<p>First I created an extension method for HtmlHelper that adds <strong>maxlength</strong> attribute to an input based on the ViewModel. If the property in the ViewModel is decorated with <strong>StringLengthAttribute</strong> annotation.</p>

<pre class="cscode"><code><span class="key">public</span> <span class="key">static</span> MvcHtmlString TextBoxForMaxLength&lt;TModel, TProperty&gt;(<span class="key">this</span> HtmlHelper&lt;TModel&gt; htmlHelper, Expression&lt;Func&lt;TModel, TProperty&gt;&gt; expression, <span class="key">object</span> htmlAttributes)
        {
            <span class="key">var</span> member = expression.Body <span class="key">as</span> MemberExpression;
            <span class="key">var</span> stringLength = member.Member
                .GetCustomAttributes(<span class="key">typeof</span>(StringLengthAttribute), <span class="key">false</span>)
                .FirstOrDefault() <span class="key">as</span> StringLengthAttribute;

            <span class="key">var</span> attributes = (IDictionary&lt;<span class="key">string</span>, <span class="key">object</span>&gt;)<span class="key">new</span> RouteValueDictionary(htmlAttributes);
            <span class="key">if</span> (stringLength != <span class="key">null</span>)
            {
                attributes.Add(<span class="str">"maxlength"</span>, stringLength.MaximumLength);
            }
            <span class="key">return</span> htmlHelper.TextBoxFor(expression, attributes);
        }</code></pre>


<h2 class="section-heading">The HtmlHelper Mock</h2>

<p>To make testing possible I created this HtmlHelper mock. The method contains much more code then needed to test this simple html extension but it provides a good base for testing other stuff like returnning partial views from extension methods</p>

<pre class="cscode"><code><span class="key">public</span> <span class="key">class</span> HtmlHelperMock
    {
        <span class="key">private</span> MockManager _mockManager;

        <span class="key">public</span> HtmlHelper GetHtmlHelper(<span class="key">string</span> viewName, <span class="key">string</span> routeController = <span class="str">""</span>,
            <span class="key">string</span> routeAction = <span class="str">""</span>)
        {
            <span class="key">var</span> routeData = <span class="key">new</span> RouteData();
            routeData.Values[<span class="str">"controller"</span>] = routeController;
            routeData.Values[<span class="str">"action"</span>] = routeAction;

            <span class="key">var</span> httpContext = MockRepository.GenerateStub&lt;HttpContextBase&gt;();
            <span class="key">var</span> viewContext = MockRepository.GenerateStub&lt;ViewContext&gt;();
            <span class="key">var</span> httpRequest = MockRepository.GenerateStub&lt;HttpRequestBase&gt;();
            <span class="key">var</span> httpResponse = MockRepository.GenerateStub&lt;HttpResponseBase&gt;();

            httpContext.Stub(c =&gt; c.Request).Return(httpRequest).Repeat.Any();
            httpContext.Stub(c =&gt; c.Response).Return(httpResponse).Repeat.Any();
            httpResponse.Stub(r =&gt; r.ApplyAppPathModifier(Arg&lt;<span class="key">string</span>&gt;.Is.Anything))
                        .Return(<span class="key">string</span>.Format(<span class="str">"/{0}/{1}"</span>, routeController, routeAction));

            viewContext.HttpContext = httpContext;
            viewContext.RequestContext = <span class="key">new</span> RequestContext(httpContext, routeData);
            viewContext.RouteData = routeData;
            viewContext.ViewData = MockRepository.GenerateStub&lt;ViewDataDictionary&gt;();
            viewContext.ViewData.Model = <span class="key">null</span>;
            viewContext.TempData = MockRepository.GenerateStub&lt;TempDataDictionary&gt;();
            viewContext.View = SetupView(viewName);

            <span class="key">return</span> <span class="key">new</span> HtmlHelper(viewContext, <span class="key">new</span> ViewPage());
        }

        <span class="key">private</span> IView SetupView(<span class="key">string</span> viewName)
        {
            <span class="key">var</span> view = MockRepository.GenerateStub&lt;IView&gt;();
            <span class="key">var</span> engine = MockRepository.GenerateStub&lt;IViewEngine&gt;();
            <span class="key">var</span> viewEngineResult = <span class="key">new</span> ViewEngineResult(view, engine);
            engine.Stub(x =&gt; x.FindPartialView(<span class="key">null</span>, viewName, <span class="key">false</span>)).IgnoreArguments().Return(viewEngineResult);
            ViewEngines.Engines.Clear();
            ViewEngines.Engines.Add(engine);
            <span class="key">return</span> view;
        }

        <span class="key">public</span> HtmlHelper&lt;TModel&gt; GetHtmlHelperForModel&lt;TModel&gt;(TModel inputDictionary, <span class="key">string</span> routeController = <span class="str">""</span>,
            <span class="key">string</span> routeAction = <span class="str">""</span>, <span class="key">string</span> viewName = <span class="str">""</span>)
        {
            <span class="key">var</span> viewData = <span class="key">new</span> ViewDataDictionary&lt;TModel&gt;(inputDictionary);
            <span class="key">var</span> routeData = <span class="key">new</span> RouteData();
            routeData.Values[<span class="str">"controller"</span>] = routeController;
            routeData.Values[<span class="str">"action"</span>] = routeAction;

            <span class="key">var</span> httpContext = MockRepository.GenerateStub&lt;HttpContextBase&gt;();
            <span class="key">var</span> viewContext = MockRepository.GenerateStub&lt;ViewContext&gt;();
            <span class="key">var</span> httpRequest = MockRepository.GenerateStub&lt;HttpRequestBase&gt;();
            <span class="key">var</span> httpResponse = MockRepository.GenerateStub&lt;HttpResponseBase&gt;();

            httpContext.Stub(c =&gt; c.Request).Return(httpRequest).Repeat.Any();
            httpContext.Stub(c =&gt; c.Response).Return(httpResponse).Repeat.Any();
            httpResponse.Stub(r =&gt; r.ApplyAppPathModifier(Arg&lt;<span class="key">string</span>&gt;.Is.Anything))
                        .Return(<span class="key">string</span>.Format(<span class="str">"/{0}/{1}"</span>, routeController, routeAction));

            viewContext.HttpContext = httpContext;
            viewContext.RequestContext = <span class="key">new</span> RequestContext(httpContext, routeData);
            viewContext.RouteData = routeData;
            viewContext.ViewData = MockRepository.GenerateStub&lt;ViewDataDictionary&gt;();
            viewContext.ViewData.Model = inputDictionary;
            viewContext.TempData = MockRepository.GenerateStub&lt;TempDataDictionary&gt;();
            viewContext.View = SetupView(viewName);

            <span class="key">return</span> <span class="key">new</span> HtmlHelper&lt;TModel&gt;(viewContext, GetViewDataContainer(viewData));
        }

        <span class="key">public</span> IViewDataContainer GetViewDataContainer(ViewDataDictionary viewData)
        {
            _mockManager = <span class="key">new</span> MockManager();
            <span class="key">var</span> mockContainer = _mockManager.Mock&lt;IViewDataContainer&gt;();
            mockContainer.Stub(c =&gt; c.ViewData).Return(viewData);
            <span class="key">return</span> mockContainer;
        }
    }</code></pre>

<h2 class="section-heading">Creating the tests</h2>

<p>Now we are able to mock and test the extension method. I'm using NUnit as my testing framework but it can be easily refactored for other frameworks. For my ViewModel I created a dummy class <strong>MyClass</strong> and added a property that contains <strong>StringLengthAttribute</strong> annotation so we can test the maxlength value set on the property. I also test if the value is being set on the input</p>

<pre class="cscode"><code>[TestFixture]
<span class="key">public</span> <span class="key">class</span> AndMaxLengthAttributeIsSet
{
    <span class="key">private</span> HtmlHelper&lt;MyClass&gt; _htmlHelper;
    <span class="key">private</span> MyClass _myClass;
    <span class="key">private</span> MvcHtmlString _htmlInput;

    [SetUp]
    <span class="key">public</span> <span class="key">void</span> Setup()
    {
        _myClass = <span class="key">new</span> MyClass
        {
            Name = <span class="str">"This is the Name Value"</span>
        };
        <span class="key">var</span> htmlMock = <span class="key">new</span> HtmlHelperMock();
        _htmlHelper = htmlMock.GetHtmlHelperForModel(_myClass);
        _htmlInput = _htmlHelper.TextBoxForMaxLength(x =&gt; x.Name, <span class="key">null</span>);
    }

    [Test]
    <span class="key">public</span> <span class="key">void</span> ShouldNotBeEmpty()
    {
        Assert.AreNotEqual(MvcHtmlString.Empty, _htmlInput);
    }

    [Test]
    <span class="key">public</span> <span class="key">void</span> ShouldSetValueInInput()
    {
        StringAssert.Contains(<span class="key">string</span>.Format(<span class="str">"value=\"{0}\""</span>, _myClass.Name), _htmlInput.ToString());
    }

    [Test]
    <span class="key">public</span> <span class="key">void</span> ShouldHaveMaxLengthAttribute()
    {
        StringAssert.Contains(<span class="key">string</span>.Format(<span class="str">"maxlength=\"{0}\""</span>, 100), _htmlInput.ToString());
    }
}

<span class="key">public</span> <span class="key">class</span> MyClass
{
    [StringLength(100)]
    <span class="key">public</span> <span class="key">string</span> Name { <span class="key">get</span>; <span class="key">set</span>; }
}</code></pre>

<h2 class="section-heading">Final thoughts</h2>

<p>I lost a couple of hours trying to mock the HtmlHelper but finally I was able to create a really nice mocked version that I think covers a lot of real test cases such as returning partial views, redirections and rendering Html from an Html extension.</p>
<p>Good coding!!</p>