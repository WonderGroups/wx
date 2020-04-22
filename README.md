# weixin-getOpenId-silent
# 微信静默获取openId

    1.http请求
    @RequestMapping(value = "/wx-index")
    public void wxIndex(String callback, HttpServletRequest request, HttpServletResponse response) throws IOException {
        System.out.println("/p/wx-index?callback===================="+callback);
        response.sendRedirect(String.format(
                "https://open.weixin.qq.com/connect/oauth2/authorize?appid=%s&redirect_uri=%s&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect",
                wxPayBean.getAppId(), "http://XXXXXXXX/Api/h5/m/costPay/p/getOpenId" + (!StringUtil.isNullOrEmpty(callback) ? URLEncoder.encode("?callback=" + callback, "UTF-8") : "")));
    }
    
    2.微信回调接口，重定向到业务位置。。。
    @RequestMapping("/getOpenId")
    public String getOpenId(HttpServletRequest req, HttpServletResponse response,
                                  String callback,String code) throws IOException {
        response.setContentType("text/html");
        response.setCharacterEncoding("UTF-8");
        String appId = "wx8d6bda6c14368487";
        String appSecret = "d388f3fc921ebd21e007f8507bf151f2";
        String CODE = req.getParameter("code");
        System.out.println("CODE----CODE" + CODE);
        System.out.println("state----state");
        String getOpenIDUrl = "https://api.weixin.qq.com/sns/oauth2/access_token?"
                + "appid=APPID"
                + "&secret=Secret"
                + "&code=" + CODE + ""
                + "&grant_type=authorization_code";

        getOpenIDUrl = getOpenIDUrl.replace("APPID", appId);
        getOpenIDUrl = getOpenIDUrl.replace("Secret", appSecret);
        System.out.println("getOpenIDUrl----getOpenIDUrl" + getOpenIDUrl);

        //JSONObject jsonObject = HttpRequest(getOpenIDUrl, "GET", null);
        HttpClient httpClient = new DefaultHttpClient();
        HttpGet httpGet = new HttpGet(getOpenIDUrl);
        ResponseHandler<String> responseHandler = new BasicResponseHandler();
        //send wx and response
        String response1 = null;
        try {
            response1 = httpClient.execute(httpGet, responseHandler);
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("=========================get openId===================");
        System.out.println(response1);
        JsonParser parser = new JsonParser();
        JsonObject jsonObject = (JsonObject) parser.parse(response1);
        String openId = jsonObject.get("openid").getAsString();
        System.out.println("=========================openId===================" + openId);
        System.out.println("=========================callback1===================" + callback);
        if(!StringUtil.isNullOrEmpty(callback)) {
            response.sendRedirect(""+callback+"?openId="+openId);
            System.out.println("=========================callback===================" + callback);
            return null;
        }else {
            return "redirect:http://xxxxxxxxx/#/merchant/account?openId="+openId;
        }
    }
