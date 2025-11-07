in every project I have a **common/configure/config.go** (example, base on each project it will vary)
```
package configure

import (
	"fmt"
	"time"

	"github.com/caarlos0/env/v11"
	"github.com/joho/godotenv"
	"github.com/rs/zerolog/log"
)

var (
	config                     *Configuration
	surveySharedUserRegionsMap map[string]struct{}
	surveySharedUserLevelsMap  map[string]struct{}
)

type Configuration struct {
	Host                            string        `env:"HOST" envDefault:"0.0.0.0"`
	Port                            string        `env:"PORT" envDefault:"8199"`
	TokenType                       string        `env:"TOKEN_TYPE" envDefault:"Bearer"`
	TokenPublicKey                  string        `env:"TOKEN_PUBLIC_KEY_PATH,file" envDefault:"certs/public.pem" envExpand:"true"`
	TokenPrivateKey                 string        `env:"TOKEN_PRIVATE_KEY_PATH,file" envDefault:"certs/private.pem" envExpand:"true"`
	MongoDBSurveyUri                string        `env:"MONGODB_SURVEY_URI" envDefault:"mongodb://localhost:27017"`
	MongoDBSurveyName               string        `env:"MONGODB_SURVEY_NAME" envDefault:"db_survey"`
	OAuth2ClientId                  string        `env:"OAUTH2_CLIENT_ID" envDefault:"!change_me!"`
	OAuth2SecretKey                 string        `env:"OAUTH2_SECRET_KEY" envDefault:"!change_me!"`
	OAuth2AuthEndpoint              string        `env:"OAUTH2_AUTH_ENDPOINT" envDefault:"http://localhost:8167"`
	OAuth2RedirectUri               string        `env:"OAUTH2_REDIRECT_URI" envDefault:"http://localhost:8199"`
	OAuth2ServerURL                 string        `env:"OAUTH2_SERVER_URL" envDefault:"http://localhost:8167"`
	SurveySharedUserLevels          []string      `env:"SURVEY_SHARED_USER_LEVELS" envDefault:"level" envSeparator:","`
	SurveySharedUserRegionsFilePath []string      `env:"SURVEY_SHARED_USER_REGIONS_FILE_PATH,file" envDefault:"location/regions" envSeparator:","`
	MongoDBRequestTimeout           time.Duration `env:"MONGODB_REQUEST_TIMEOUT" envDefault:"3m"`
	AccessTokenTimeout              time.Duration `env:"ACCESS_TOKEN_TIMEOUT" envDefault:"1h"`
	RefreshTokenTimeout             time.Duration `env:"REFRESH_TOKEN_TIMEOUT" envDefault:"2h"`
	LoginSessionExpiredTime         time.Duration `env:"LOGIN_SESSION_EXPIRED_TIME" envDefault:"5m"`
	PaginationMaxItem               int64         `env:"PAGINATION_MAX_ITEM" envDefault:"50"`
	APIBodyLimitSize                int           `env:"API_BODY_LIMIT_SIZE" envDefault:"1073741824"`
	SurveyIdLength                  int           `env:"SURVEY_ID_LENGTH" envDefault:"8"`
	SurveySharedUsersMaxItem        int           `env:"SURVEY_SHARED_USERS_MAX_ITEM" envDefault:"100"`
	SurveyParticipantsMaxItem       int           `env:"SURVEY_PARTICIPANTS_MAX_ITEM" envDefault:"500"`
	Debug                           bool          `env:"DEBUG" envDefault:"true"`
	ElasticAPMEnable                bool          `env:"ELASTIC_APM_ENABLE" envDefault:"false"`
	MongoAutoIndexing               bool          `env:"MONGO_AUTO_INDEXING" envDefault:"false"`
	OAuth2InsecureEnable            bool          `env:"OAUTH2_INSECURE_ENABLE" envDefault:"false"`
}

func (cfg Configuration) ServerAddress() string {
	return fmt.Sprintf("%s:%s", cfg.Host, cfg.Port)
}

func GetSurveySharedUserRegionsMap() map[string]struct{} {
	return surveySharedUserRegionsMap
}

func GetSurveySharedUserLevelsMap() map[string]struct{} {
	return surveySharedUserLevelsMap
}

func initMapping() {
	surveySharedUserLevelsMap = make(map[string]struct{}, len(config.SurveySharedUserLevels))
	for _, level := range config.SurveySharedUserLevels {
		surveySharedUserLevelsMap[level] = struct{}{}
	}
	surveySharedUserRegionsMap = make(map[string]struct{}, len(config.SurveySharedUserRegionsFilePath))
	for _, region := range config.SurveySharedUserRegionsFilePath {
		surveySharedUserRegionsMap[region] = struct{}{}
	}
}

func GetConfig() Configuration {
	if config == nil {
		_ = godotenv.Load()
		config = &Configuration{}
		if err := env.Parse(config); err != nil {
			log.Fatal().Err(err).Msg("Get Config Error")
		}
		initMapping()
	}
	return *config
}

```

Then I need to make **.env** with default value = **envDefault**: 
```
HOST=0.0.0.0
PORT=8199
.etc
```
And I will have a **README.md**: 
```
| Environment                          | Default                   | Separator |  
|--------------------------------------|---------------------------|-----------|  
| HOST                                 | 0.0.0.0                   |           |  
| PORT                                 | 8190                      |           |  
| TOKEN_TYPE                           | Bearer                    |           |  
| TOKEN_PUBLIC_KEY_PATH                | certs/public.pem          |           |  
| TOKEN_PRIVATE_KEY_PATH               | certs/private.pem         |           |  
| MONGODB_SURVEY_URI                   | mongodb://localhost:27017 |           |  
| MONGODB_SURVEY_NAME                  | db_survey                 |           |  
| OAUTH2_CLIENT_ID                     | !change_me!               |           |  
| OAUTH2_SECRET_KEY                    | !change_me!               |           |  
| OAUTH2_AUTH_ENDPOINT                 | http://localhost:8167     |           |  
| OAUTH2_REDIRECT_URI                  | http://localhost:8199     |           |  
| OAUTH2_SERVER_URL                    | http://localhost:8167     |           |  
| MONGODB_REQUEST_TIMEOUT              | 3m                        |           |  
| ACCESS_TOKEN_TIMEOUT                 | 1h                        |           |  
| LOGIN_SESSION_EXPIRED_TIME           | 5m                        |           |  
| PAGINATION_MAX_ITEM                  | 50                        |           |  
| API_BODY_LIMIT_SIZE                  | 1073741824                |           |  
| DEBUG                                | true                      |           |  
| ELASTIC_APM_ENABLE                   | false                     |           |  
| OAUTH2_INSECURE_ENABLE               | false                     |           |  
| SURVEY_ID_LENGTH                     | 8                         |           |  
| SURVEY_SHARED_USERS_MAX_ITEM         | 100                       |           |  
| SURVEY_PARTICIPANTS_MAX_ITEM         | 500                       |           |  
| SURVEY_SHARED_USER_LEVELS            | level                     | ,         |  
| SURVEY_SHARED_USER_REGIONS_FILE_PATH | location/regions          | ,         |
```
Now I want to write a command line tool that can: 
- Generate .env file from struct Configure from config.go
- Genereate README.md from struct Configure from config.go
Note: 
- Write in Go
- It usages: 
	- tool-gen --type=env config.go .env
	- tool-gen --type=md config.go README.md
- file config.go will always have a Configure struct like example above

Let's: Give me some options to make this tool 