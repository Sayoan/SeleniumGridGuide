# Configuração Selenium Grid
O Selenium-Grid permite que você execute seus testes em diferentes máquinas em diferentes navegadores em paralelo. Ou seja, executando vários testes ao mesmo tempo em diferentes máquinas executando diferentes navegadores e sistemas operacionais. 

Baixar o arquivo do selenium server
```sh
https://www.seleniumhq.org/download/
```
Colocar o Jar em C: e abrir o CMD na pasta (Shift+Botão Direito)

Executar o comando via CMD
```sh
 java -jar seleniumserver.jar -role hub
 ```
Caso a porta esteja ocupada use:
```sh
 java -jar seleniumserver.jar -port 4445 -role hub
```
Acessar o servidor via navegador e verificar se o HUB está conectado
```sh
Link http://localhost:4444/grid/console
```

- Cadastro de nó

```sh
java -jar seleniumserver.jar -role webdriver -hub https://IPHUB:PORTA/grid/register
```

Configurando o Selenium GRID - HUB com arquivo JSON (O arquivo deve estar na pasta que irá executar o comando)
```sh
java -jar "seleniumserver.jar" -port 4444 -role hub -hubConfig HubConfig.json
```
Conteúdo Arquivo HUBConfig.JSON
```sh
{
	  "port": 4444,
	  "newSessionWaitTimeout": -1,
	  "servlets" : [],
	  "withoutServlets": [],
	  "custom": {},
	  "capabilityMatcher": "org.openqa.grid.internal.utils.DefaultCapabilityMatcher",
	  "registryClass": "org.openqa.grid.internal.DefaultGridRegistry",
	  "throwOnCapabilityNotPresent": true,
	  "cleanUpCycle": 5000,
	  "role": "hub",
	  "debug": false,
	  "browserTimeout": 0,
	  "timeout": 1800
}
```
Configurando o Selenium GRID - Nó com arquivo JSON (O arquivo deve estar na pasta que irá executar o comando)
```sh
java -Dwebdriver.chrome.driver="chromedriver.exe" -Dwebdriver.ie.driver="IEDriverServer.exe" -Dwebdriver.gecko.driver="geckodriver.exe" -jar seleniumserver.jar -role node -nodeConfig NodeDefaultConfig.json
```
Conteúdo Arquivo NodeDeafultConfig.JSON (JUNIT 3 acima)
```sh
{
  "capabilities":
  [
    {
      "browserName": "firefox",
      "marionette": true,
      "maxInstances": 5,
      "seleniumProtocol": "WebDriver"
    },
     {
      "browserName": "internet explorer",
      "marionette": true,
      "maxInstances": 5,
      "seleniumProtocol": "WebDriver"
    },
    {
      "browserName": "chrome",
      "maxInstances": 5,
      "seleniumProtocol": "WebDriver"
    }
  ],
  "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
  "maxSession": 5,
  "port": -1,
  "register": true,
  "registerCycle": 5000,
  "hub": "http://localhost:4444",
  "nodeStatusCheckTimeout": 5000,
  "nodePolling": 5000,
  "role": "node",
  "unregisterIfStillDownAfter": 60000,
  "downPollingLimit": 2,
  "debug": false,
  "servlets" : [],
  "withoutServlets": [],
  "custom": {},
  "browserTimeout": 0,
  "timeout": 1800
}
```
# Configuração do WebDriver com o RemoteWebDriver 
Configurar via AppConfig a key responsável pelo NavegadorDefault e IpHub.
```
[SetUp]
        public void SetUpGrid() //GRID
        {
            //criado um appconfig com a configuração desejada
            string navegador = ConfigurationManager.AppSettings["NavegadorDefault"].ToString();
            string nodeURL = ConfigurationManager.AppSettings["IpHub"].ToString();
            string local = ConfigurationManager.AppSettings["Local"].ToString();

            switch (local)
            {
                case ("true"): //rodar local
                    _driver = new ChromeDriver(SeleniumUteis.SeleniumUteis.getPathSeleniumDriver());
                  

                    break;

                case ("false"): //rodar grid
                    switch (navegador)
                    {
                        case ("firefox"):
                            FirefoxOptions firefox = new FirefoxOptions();
                            _driver = new RemoteWebDriver(new Uri(nodeURL), firefox.ToCapabilities());
                            break;

                        case ("chrome"):
                            ChromeOptions chrome = new ChromeOptions();
                            _driver = new RemoteWebDriver(new Uri(nodeURL), chrome.ToCapabilities());
                            break;

                        case ("ie"):
                            InternetExplorerOptions ie = new InternetExplorerOptions();
                            _driver = new RemoteWebDriver(new Uri(nodeURL), ie.ToCapabilities());
                            break;

                    }
                    break;


                    
            }
            _driver.Navigate().GoToUrl(Credentials.URL);

            //Ação que maximiza a tela
            _driver.Manage().Window.Maximize();

        }
```
