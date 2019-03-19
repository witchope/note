# Spring MVC 源码浅析

![RequestLifecycle](../assets/RequestLifecycle.png)

主要实现逻辑在于下面的 `doDispath` 方法

```java
/**
    * Process the actual dispatching to the handler.
    * 分发请求给 handler
    * <p>The handler will be obtained by applying the servlet's HandlerMappings in order.
    * 根据 HandlerMapping (list) 中的顺序进行加载 handler
    * The HandlerAdapter will be obtained by querying the servlet's installed HandlerAdapters
    * 通过请求当前 Serverlet 中 HandlerAdapters（list） 获取
    * to find the first that supports the handler class.
    * 匹配当前 handler 的 第一个适配器（HandlerAdapter）
    * <p>All HTTP methods are handled by this method. It's up to HandlerAdapters or handlers
    * 所有的 HTTP 方法都会经过该方法处理，不过最终取决与 HandlerAdapters 或者 handlers
    * themselves to decide which methods are acceptable.
    * 它们本身决定那个方法被采纳
    *
    * @param request current HTTP request
    * @param response current HTTP response
    * @throws Exception in case of any kind of processing failure
    */
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;

    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
        ModelAndView mv = null;
        Exception dispatchException = null;

        try {
            processedRequest = checkMultipart(request);
            multipartRequestParsed = (processedRequest != request);

            // Determine handler for the current request.
            // 调用 handlerMapping 获取 HandlerExecutionChain
            // 其实就是提取当前 controller 信息
            mappedHandler = getHandler(processedRequest);
            if (mappedHandler == null) {
                // 如果没找到对应的handler则通过response返回错误信息
                noHandlerFound(processedRequest, response);
                return;
            }

            // Determine handler adapter for the current request.
            // 获取当前请求的 handler 的适配器
            HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

            // Process last-modified header, if supported by the handler.
            // 处理 last-modified 请求头缓存,客户端第一次访问 url 时会添加一个 last-modified 的响应头,客户端第二次访问 url 时
            // 客户端会向服务器发送请求头 "if-modified-since" ，询问服务器该时间之后当前请求的内容是否修改过
            //如果无变化，则自动返回 304 状态码，只要响应头，内容为空，节省服务器网络带宽
            String method = request.getMethod();
            boolean isGet = "GET".equals(method);
            if (isGet || "HEAD".equals(method)) {
                long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                if (logger.isDebugEnabled()) {
                    logger.debug("Last-Modified value for [" + getRequestUri(request) + "] is: " + lastModified);
                }
                if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
                    return;
                }
            }

            // 拦截器拦截请求前置处理
            if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                return;
            }

            // Actually invoke the handler.
            // 点用 handler 处理返回 ModelAndView
            // 调用 controller 进行处理
            mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

            if (asyncManager.isConcurrentHandlingStarted()) {
                return;
            }
            // applyDefaultViewName 方法则是当 mv 中没有 view 的值时， 采用之前初始化时这个方法中提供的信息
            applyDefaultViewName(processedRequest, mv);
            mappedHandler.applyPostHandle(processedRequest, response, mv);
        }
        catch (Exception ex) {
            dispatchException = ex;
        }
        catch (Throwable err) {
            // As of 4.3, we're processing Errors thrown from handler methods as well,
            // making them available for @ExceptionHandler methods and other scenarios.
            dispatchException = new NestedServletException("Handler dispatch failed", err);
        }
        processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
    }
    catch (Exception ex) {
        triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
    }
    catch (Throwable err) {
        triggerAfterCompletion(processedRequest, response, mappedHandler,
                new NestedServletException("Handler processing failed", err));
    }
    finally {
        if (asyncManager.isConcurrentHandlingStarted()) {
            // Instead of postHandle and afterCompletion
            if (mappedHandler != null) {
                mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
            }
        }
        else {
            // Clean up any resources used by a multipart request.
            if (multipartRequestParsed) {
                cleanupMultipart(processedRequest);
            }
        }
    }
}
```
