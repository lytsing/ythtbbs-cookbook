# HT的cgi处理过程

主要是`cgi-bin/www`文件处理客户请求的过程
用 http://domain/SMAGIC/boa 这样子。。这样的 cgi 对应的程序就是 nju09/ 下的 bbsboa.c

apache的httpd.conf里设置了cgi-bin的目录路径和调用的可执行处理文件 www，然后客户端浏览器发送的请求(形如 http://your_domain/SMGICXXXX )就直接通过 apache 调用 www 进行处理。

一、分析cgi环境变量 SCRIPT_URL 在`BBSLIB.c`里，url_parse()函数定义：

```
int
url_parse()
{
	char *url, *end, name[STRLEN], *p, *extraparam;
	url = getenv("SCRIPT_URL");
	if (NULL == url) {
		char buf[1024];
		url = getenv("REQUEST_URI");
		if (NULL == url)
			return -1;
		strsncpy(buf, url, sizeof (buf));
		if ((end = strchr(buf, '?')))
			*end = 0;
		setenv("SCRIPT_URL", buf, 0);
	}
	strcpy(name, "/");
#ifdef USESESSIONCOOKIE
	if (strcmp(url, "/")) {
		strsncpy(name, getparm("SESSION"), sizeof (name));
	}
	if (!strncmp(url, "/" SMAGIC, sizeof (SMAGIC))) {
		//兼容 telnet
		if (!name[1]) {
			snprintf(name, STRLEN, "%s", url + sizeof (SMAGIC));
			p = strchr(name, '/');
			if (p)
				*p = 0;
		}

		p = strchr(url + sizeof (SMAGIC), '/');
		if (NULL != p) {
			url = strchr(url + 1, '/');
		} else
			return -1;

	}
#else
	if (!strncmp(url, "/" SMAGIC, sizeof (SMAGIC))) {
		snprintf(name, STRLEN, "%s", url + sizeof (SMAGIC));
		p = strchr(name, '/');
		if (NULL != p) {
			*p = 0;
			url = strchr(url + 1, '/');
		} else
			return -1;
	}
#endif
	extraparam = strchr(name, '_');
	if (extraparam) {
		*extraparam = 0;
		extraparam++;
	} else
		extraparam = "";
	extraparam_init(extraparam);
	loginok = user_init(&currentuser, &u_info, name);
	if (loginok)
		w_info = &(u_info->wwwinfo);
	else
		w_info = &guest;

	snprintf(needcgi, STRLEN, "%s", url + 1);
	if ((end = strchr(needcgi, '?')) != NULL)
		*end = 0;

	if (!*needcgi || nologin) {
		strcpy(needcgi, "bbsindex");
		strcpy(rframe, "");
		strsncpy(name, getsenv("HTTP_HOST"), 70);
		p = strchr(name, '.');
		if (p != NULL && isaword(domainname, p + 1)) {
			*p = 0;
			strsncpy(rframe, name, 60);
		}
		if (rframe[0] && isaword(specname, rframe))
			rframe[0] = 0;
		return 0;
	}
	if (NULL != (end = strchr(needcgi, '/')))
		*end = 0;
	return 0;
}
```

二、bbsmain.c 里这样调用 url_parse()

```
int
main(int argc, char *argv[])
{
	struct cgi_applet *a = NULL;
	struct rlimit rl;
	int i;
	struct in_addr in;
	seteuid(BBSUID);
	setuid(BBSUID);
	setgid(BBSGID);
	cgi_time(NULL);
	rl.rlim_cur = 100 * 1024 * 1024;
	rl.rlim_max = 100 * 1024 * 1024;
	setrlimit(RLIMIT_CORE, &rl);
	thispid = getpid();
	now_t = time(NULL);
	srand(now_t * 2 + thispid);
	if (initbbsinfo(&bbsinfo) < 0)
		exit(0);
	if (uhash_uptime() == 0) {
		exit(-1);
	}
	wwwcache = bbsinfo.wwwcache;
	thisversion = file_time(argv[0]);
#ifndef USEBIG5
	if (thisversion > wwwcache->www_version)
		wwwcache->www_version = thisversion;
#endif
	html_header(0);
	if (geteuid() != BBSUID)
		http_fatal("uid error.");
	chdir(BBSHOME);
	shm_init(&bbsinfo);
	signal(SIGTERM, wantquit);
	if (access("NOLOGIN", F_OK))
		nologin = 0;
	get_att_server();
	get_tjpg_server();
#ifdef ENABLE_FASTCGI
	atexit(FCGI_Finish);
#endif
	while (1) {
		accepting = 1;
		if (shouldExit || wwwcache->www_version > thisversion
		    || rt++ > 400000) {
			accepting = 0;
			break;
		}
		if (FCGI_Accept() < 0) {
			accepting = 0;
			break;
		}
		accepting = 0;
#ifdef USEBIG5
		start_outcache();
#endif
		cginame = NULL;
		if (setjmp(cgi_start)) {
#ifdef USEBIG5
			end_outcache();
#endif
			cgi_time(a);
			continue;
		}
		html_header(0);
		hsprintf(NULL, NULL);
		now_t = time(NULL);
		via_proxy = 0;
		strsncpy(realfromhost, getsenv("REMOTE_ADDR"),
			 sizeof (realfromhost));
		inet_aton(realfromhost, &from_addr);
		in = from_addr;
		in.s_addr &= 0x0000ffff;
		strsncpy(fromhost, inet_ntoa(in), sizeof (fromhost));
		for (i = 0; wwwcache->validproxy[i] && i < MAX_PROXY_NUM; i++) {
			if (from_addr.s_addr == wwwcache->validproxy[i]) {
				via_proxy = 1;
				break;
			}
		}
		if (via_proxy
		    || from_addr.s_addr == wwwcache->accel_addr.s_addr) {
			char *ptr, *p;
			ptr = getenv("HTTP_X_FORWARDED_FOR");
			if (!ptr)
				ptr = getsenv("REMOTE_ADDR");
			p = strrchr(ptr, ',');
			if (p != NULL) {
				while (!isdigit(*p) && *p)
					p++;
				if (*p)
					strsncpy(realfromhost, p,
						 sizeof (realfromhost));
				else
					strsncpy(realfromhost, ptr,
						 sizeof (realfromhost));
			} else
				strsncpy(realfromhost, ptr,
					 sizeof (realfromhost));
			inet_aton(realfromhost, &from_addr);
			in = from_addr;
			in.s_addr &= 0x0000ffff;
			strsncpy(fromhost, inet_ntoa(in), sizeof (fromhost));
		}
		http_parm_init();
		if (url_parse())
			http_fatal("%s 没有实现的功能1!",
				   getsenv("SCRIPT_URL"));
		brc_expired();
		a = get_cgi_applet(needcgi);
		if (a != NULL) {
			cginame = a->name[0];
			//access(getsenv("QUERY_STRING"), F_OK);
			wwwcache->www_visit++;
			(*(a->main)) ();
#ifdef USEBIG5
			end_outcache();
#endif
			cgi_time(a);
			continue;
		}
		http_fatal("%s 没有实现的功能2!", getsenv("SCRIPT_URL"));
//              end_outcache();
	}
	logtimeused();
	exit(5);
}
```

三、main()调用url_parse()以后，看url_parse()里面大略做的是：
先得到环境变量SCRIPT_URL的值，如上面的就是SCRIPT_URL="/SMAGIC/boa"
然后，截掉前面的部分，把剩下的boa赋给needcgi，然后返回main()，
然后main()以needcgi为参数调用get_cgi_applet(),根据needcgi从applets表中查到函数名

```
struct cgi_applet *
get_cgi_applet(char *needcgi)
{
	static ght_hash_table_t *p_table = NULL;
	struct cgi_applet *a;

	if (p_table == NULL) {
		int i;
		a = applets;
		p_table = ght_create(250);
		while (a->main != NULL) {
			a->count = 0;
			a->utime = 0;
			a->stime = 0;
			for (i = 0; a->name[i] != NULL; i++)
				ght_insert(p_table, (void *) a,
					   strlen(a->name[i]),
					   (void *) a->name[i]);
			a++;
		}
	}
	if (p_table == NULL)
		return NULL;
	return ght_get(p_table, strlen(needcgi), needcgi);
}
#else
struct cgi_applet *
get_cgi_applet(char *needcgi)
{
	struct cgi_applet *a;
	int i;
	a = applets;
	while (a->main != NULL) {
		for (i = 0; a->name[i] != NULL; i++)
			if (!strcmp(needcgi, a->name[i]))
				return a;
		a++;
	}
	return NULL;
}
#endif
```

既然url_parse()这样写的，http://domain/cgi-bin/www 自然不行啦..


四、据needcgi从applets表中查到函数名，以下是可供/var/www/cgi-bin/下的可执行文件www调用的内部函数：

```
struct cgi_applet applets[] = {
//      {bbsusr_main, {"bbsusr", NULL}},
	{bbsucss_main, {"bbsucss", NULL}},
	{bbsdefcss_main, {"bbsdefcss", NULL}},
	{bbstop10_main, {"bbstop10", NULL}},
	{bbsdoc_main, {"bbsdoc", "doc", NULL}},
	{bbscon_main, {"bbscon", "con", NULL}},
	{bbsbrdadd_main, {"bbsbrdadd", "brdadd", NULL}},
	{bbsboa_main, {"bbsboa", "boa", NULL}},
	{bbsall_main, {"bbsall", NULL}},
	{bbsanc_main, {"bbsanc", "anc", NULL}},
	{bbs0an_main, {"bbs0an", "0an", NULL}},
	{bbslogout_main, {"bbslogout", NULL}},
	{bbsleft_main, {"bbsleft", NULL}},
	{bbslogin_main, {"bbslogin", NULL}},
	{bbsbadlogins_main, {"bbsbadlogins", NULL}},
	{bbsqry_main, {"bbsqry", "qry", NULL}},
	{bbsnot_main, {"bbsnot", "not", NULL}},
	{bbsfind_main, {"bbsfind", NULL}},
	{bbsfadd_main, {"bbsfadd", NULL}},
	{bbsfdel_main, {"bbsfdel", NULL}},
	{bbsfall_main, {"bbsfall", NULL}},
	{bbsfriend_main, {"bbsfriend", NULL}},
	{bbsfoot_main, {"bbsfoot", NULL}},
	{bbsform_main, {"bbsform", NULL}},
	{bbspwd_main, {"bbspwd", NULL}},
	{bbsplan_main, {"bbsplan", NULL}},
	{bbsinfo_main, {"bbsinfo", NULL}},
	{bbsmypic_main, {"bbsmypic", "mypic", NULL}},
	{bbsmybrd_main, {"bbsmybrd", NULL}},
	{bbssig_main, {"bbssig", NULL}},
	{bbspst_main, {"bbspst", "pst", NULL}},
	{bbsgcon_main, {"bbsgcon", "gcon", NULL}},
	{bbsgdoc_main, {"bbsgdoc", "gdoc", NULL}},
	{bbsdel_main, {"bbsdel", "del", NULL}},
	{bbsdelmail_main, {"bbsdelmail", NULL}},
	{bbsmailcon_main, {"bbsmailcon", NULL}},
	{bbsmail_main, {"bbsmail", "mail", NULL}},
	{bbsdelmsg_main, {"bbsdelmsg", NULL}},
	{bbssnd_main, {"bbssnd", NULL}},
	{bbsnotepad_main, {"bbsnotepad", NULL}},
	{bbsmsg_main, {"bbsmsg", NULL}},
	{bbssendmsg_main, {"bbssendmsg", NULL}},
#ifdef ENABLE_EMAILREG
	{bbsreg_main, {"bbsreg", NULL}},
	{bbsemailreg_main, {"bbsemailreg", NULL}},
	{bbsemailconfirm_main, {"bbsemailconfirm", "emailconfirm", NULL}},
#else
	{bbsreg_main, {"bbsreg", "bbsemailreg", NULL}},
#endif
#ifdef ENABLE_INVITATION
	{bbsinvite_main, {"bbsinvite", "invite", NULL}},
	{bbsinviteconfirm_main, {"bbsinviteconfirm", "inviteconfirm", NULL}},
#endif
	{bbsscanreg_main, {"bbsscanreg", NULL}},
	{bbsmailmsg_main, {"bbsmailmsg", NULL}},
	{bbssndmail_main, {"bbssndmail", NULL}},
	{bbsnewmail_main, {"bbsnewmail", NULL}},
	{bbspstmail_main, {"bbspstmail", "pstmail", NULL}},
	{bbsgetmsg_main, {"bbsgetmsg", NULL}},
	//{bbschat_main, {"bbschat", NULL}},
	{bbscloak_main, {"bbscloak", NULL}},
	{bbsmdoc_main, {"bbsmdoc", "mdoc", NULL}},
	{bbsnick_main, {"bbsnick", NULL}},
	{bbstfind_main, {"bbstfind", "tfind", NULL}},
	{bbsadl_main, {"bbsadl", NULL}},
	{bbstcon_main, {"bbstcon", "tcon", NULL}},
	{bbstdoc_main, {"bbstdoc", "tdoc", NULL}},
	{bbsdoreg_main, {"bbsdoreg", NULL}},
	{bbsmywww_main, {"bbsmywww", NULL}},
	{bbsccc_main, {"bbsccc", "ccc", NULL}},
	//{bbsufind_main, {"bbsufind", NULL}},
	{bbsclear_main, {"bbsclear", "clear", NULL}},
	{bbsstat_main, {"bbsstat", NULL}},
	{bbsedit_main, {"bbsedit", "edit", NULL}},
	{bbsman_main, {"bbsman", "man", NULL}},
	{bbsparm_main, {"bbsparm", NULL}},
	{bbsfwd_main, {"bbsfwd", "fwd", NULL}},
	{bbsmnote_main, {"bbsmnote", NULL}},
	{bbsdenyall_main, {"bbsdenyall", NULL}},
	{bbsdenydel_main, {"bbsdenydel", NULL}},
	{bbsdenyadd_main, {"bbsdenyadd", NULL}},
	{bbstopb10_main, {"bbstopb10", NULL}},
	{bbsbfind_main, {"bbsbfind", "bfind", NULL}},
	{bbsx_main, {"bbsx", NULL}},
	{bbseva_main, {"bbseva", "eva", NULL}},
	{bbsvote_main, {"bbsvote", "vote", NULL}},
	{bbsshownav_main, {"bbsshownav", NULL}},
	{bbsbkndoc_main, {"bbsbkndoc", "bkndoc", NULL}},
	{bbsbknsel_main, {"bbsbknsel", "bknsel", NULL}},
	{bbsbkncon_main, {"bbsbkncon", "bkncon", NULL}},
	{bbshome_main, {"bbshome", "home", NULL}},
	{bbsindex_main, {"bbsindex", "index", NULL}},
//      {bbsupload_main, {"bbsupload", NULL}},
	{bbslform_main, {"bbslform", NULL}},
	{bbslt_main, {"bbslt", NULL}},
	{bbsdt_main, {"bbsdt", NULL}},
	{regreq_main, {"regreq", NULL}},
	{bbsselstyle_main, {"bbsselstyle", NULL}},
	{bbscon1_main, {"bbscon1", "c1", NULL}},
	{bbsattach_main, {"attach", NULL}},
	{bbskick_main, {"kick", NULL}},
	{bbsshowfile_main, {"bbsshowfile", "showfile", NULL}},
	{bbsincon_main, {"boards", NULL}},
	{NULL}
};
```

