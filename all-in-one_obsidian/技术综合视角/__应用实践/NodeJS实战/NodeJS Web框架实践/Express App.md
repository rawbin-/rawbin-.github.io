# Request
## req.app
## req.baseUrl
## req.body
## req.cookie
## req.cookie
## req.fresh
## req.host
## req.hostname
## req.ip
## req.ips
## req.method
## req.originalUrl
## req.params
## req.path
## req.protocol
## req.res
## req.route
## req.secure
## req.singledCookies
## req.stale
## req.subdomains
## req.xhr
## req.accepts(types)
## req.acceptsCharsets(charset[,...])
## req.acceptsEncodings(encoding[,...])
## req.acceptsLanguage(lang[,...])
## req.get(field)
## req.is(type)
## req.param()
## req.range(size[,options])
# Response
## res.app
## res.headersSent
## res.locals
## res.req
## res.append(field[,value])
## res.attachment([filename])
## res.cookie(name,value[,options])
## res.clearCookie(name,[,options])
## res.download(path[,filename]\[,options\]\[,fn]\)
## res.end([data]\[,encoding\])
## res.format(object)
## res.get(field)
## res.json([body])
## res.jsonp([body])
## res.links(links)
## res.location(path)
## res.redirect([status,]path)
## res.render(view[,locals]\[,callback]\)
## res.send([body])
## res.sendFile(path[,options]\[,fn\])
## res.sendStatus(statusCode)
## res.set(field[,value])
## res.status(code)
## res.type(type)
## res.vary(field)
# Router
## router.all(path,[callback,...] callback)
## router.METHOD(path,[callback,...] callback)
## router.param(name,callback)
## router.use([path],[function...] function)


# Express
## express.json([options])
## express.static(root,[options])
## express.Router([options])
## express.urlencoded()

# Application
## app.locals
## app.mountpath
## app.router
## app.on('mount',callback(parent))
## app.all(path,callback[,callback])
## app.delete(path,callback[,callback])
## app.disable(name)
## app.disabled(name)
## app.enable(name)
## app.enabled(name)
## app.engine(ext, callback)
## app.get(name)
## app.get(path,callbac[,callback])
## app.listen(path[,callback])
## app.listen([port[,host[,backlog]]]\[,callback]\)
## app.METHOD(path,callback[,callback])
## app.param(name,callback)
## app.path()
## app.post(path,callback[,callback])
## app.put(path,callback[,callback])
## app.render(view,[locals],calback)
## app.route(path)
## app.set(name,value)
## app.use([path,]callback[,callback])