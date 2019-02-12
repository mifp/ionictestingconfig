# Ionic Testing Config and Setup

## The setup
This setup is for creating and running unit tests with your Ionic application using [Jasmine](https://jasmine.github.io/) and [Karma](https://karma-runner.github.io/latest/index.html).


## Install dev dependencies

    npm install --save-dev @angular/cli @angular/router @types/jasmine @types/node ionic-mocks jasmine-core jasmine-spec-reporter karma karma-chrome-launcher karma-cli karma-jasmine karma-jasmine-html-reporter karma-coverage-istanbul-reporter karma-junit-reporter karma-spec-reporter ionic-mocks


## Modify existing Ionic config files:
Add jasmine types in Ionic’s tsconfig.json:

    "typeRoots": [
      "node_modules/@types"
    ]
    ...
    "types": [
     "jasmine"
    ]
    ...
    "exclude": [
        ...
        "**/*.spec.ts"
    ]

Add the following line to the scripts object in your package.json:

    "test": "karma start ./test/karma.conf.js",
    "test-ci": "karma start ./test/karma.conf.js --single-run",
    "test-coverage": "karma start ./test/karma.conf.js --coverage",



# Your first unit test
Pick one of your components to write a test for and create a component-name.spec.ts file for it.

A simple skeleton unit test file, testing a page, looks like this, where Page1 is whatever component you’re testing.

```javascript
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { By }           from '@angular/platform-browser';
import { DebugElement } from '@angular/core';
import { Page1 } from './page1';
import { IonicModule, Platform, NavController} from 'ionic-angular/index';
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';

describe('Page1', () => {
  let de: DebugElement;
  let comp: Page1;
  let fixture: ComponentFixture<Page1>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [Page1],
      imports: [
        IonicModule.forRoot(Page1)
      ],
      providers: [
        NavController
      ]
    });
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(Page1);
    comp = fixture.componentInstance;
    de = fixture.debugElement.query(By.css('h3'));
  });

  it('should create component', () => expect(comp).toBeDefined());

  it('should have expected <h3> text', () => {
    fixture.detectChanges();
    const h3 = de.nativeElement;
    expect(h3.innerText).toMatch(/ionic/i,
      '<h3> should say something about "Ionic"');
  });

  it('should show the favicon as <img>', () => {
    fixture.detectChanges();
    const img: HTMLImageElement = fixture.debugElement.query(By.css('img')).nativeElement;
    expect(img.src).toContain('assets/icon/favicon.ico');
  });
});
```

When testing providers (services) it should not be necessary to configure the testbed. Instead you initialise with required dependencies, which _should_ be mocked, as we are only testing this *unit*. Some ionic specific mocks can be found in the test folder, with the config files, or fetched from the node package 'ionic-mocks'.

```javascript
import { ConfigService } from './ConfigService';
import { AppVersionMock } from 'ionic-mocks';

describe('ConfigService', () => {
  let service;

  const appVersion =  AppVersionMock.instance();
        
  const callServerService: any = {
    // mock properties here 
    getData() {
        return { data: 'someData' 
        }
    }, 
    getDataAsync() {
       return new Promise(function(resolve, reject) {
                setTimeout(function() {
                    resolve('foo');
                }, 300);
    } 
    });
  }
      
  beforeEach(() => {
    service = new ConfigService(appVersion, callServerService);
  });

    
  it('should run #setEnvironment()', async () => {
    const result = service.setEnvironment('test');
    expect(result).toBeTruthy();
    expect(service.environment).toEqual('test');
  });
      
});
```



# Running the tests

To spin up a browser with karma and run your tests simply write:

    npm test



//TODO jenkins setup



# Debugging Setup
If an error occurs when running the `npm test` don't fret. There might be some config files that needs tweaking or some issues with version. So before raising an issue, please follow these basic steps to debug the setup:

- check you are on the correct node LTS
- remove and reinstall node_modules
- clone a copy of an existing project with unit test and compare the relevant files with your own using a diff tool (meld)

