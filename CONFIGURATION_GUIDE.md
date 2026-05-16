# MuleSoft Configuration Externalization Guide

This document describes the externalized configuration structure for the Switchee Insights MuleSoft application.

## Overview

All configuration parameters have been externalized from the MuleSoft flows into property files to support:
- **Environment Portability**: Easy deployment across different environments
- **Security**: Sensitive data externalized from code
- **Maintainability**: Configuration changes without code changes
- **Best Practices**: Follows MuleSoft recommended patterns

## Configuration Structure

### Main Configuration Files

| File | Purpose | Environment |
|------|---------|-------------|
| `config.properties` | Default configuration (demo environment) | Demo/Local Development |
| `config-dev.properties` | Development environment specific values | Development |
| `config-test.properties` | Test environment specific values | Test/Staging |
| `config-prod.properties` | Production environment specific values | Production |

### Configuration Categories

#### 1. HTTP Listener Configuration
```properties
# HTTP server settings
http.host=0.0.0.0
http.port=8081
```

#### 2. Switchee API Configuration
```properties
# Main Switchee API settings
switchee.api.host=api.demo.switchee.co
switchee.api.port=443
switchee.api.protocol=HTTPS
switchee.api.username=your-api-username
switchee.api.password=your-api-password
switchee.api.base.path=/v3.0

# Authentication API settings
switchee.auth.api.host=api-auth.demo.switchee.co
switchee.auth.api.port=443
switchee.auth.api.protocol=HTTPS
switchee.auth.api.token.path=/oauth2/token

# Insights API settings
switchee.insights.api.host=api.demo.switchee.co
switchee.insights.api.port=443
switchee.insights.api.protocol=HTTPS
switchee.insights.api.key=your-x-api-key
switchee.insights.api.version=v3.0
```

#### 3. Switchee OAuth Configuration
```properties
# OAuth authentication settings
switchee.oauth.client.credentials=Basic [base64-encoded-credentials]
switchee.oauth.grant.type=client_credentials
switchee.oauth.scope=https://api.demo.switchee.co/api.client
```

#### 4. Salesforce Configuration
```properties
# Salesforce connection settings
salesforce.url=https://login.salesforce.com/services/Soap/u/61.0
salesforce.username=your-username
salesforce.password=your-password

# Salesforce Data Cloud settings
salesforce.data.cloud.consumer.key=[consumer-key]
salesforce.data.cloud.keystore.path=path-to-your-keystore
salesforce.data.cloud.store.password=your-keystore-password
salesforce.data.cloud.key.alias=your-keystore-alias
salesforce.data.cloud.subject=your-dc-subject
salesforce.data.cloud.audience.url=https://login.salesforce.com
salesforce.data.cloud.source.name=your-dc-ingestion-api-source-name
salesforce.data.cloud.object.name=your-dc-ingestion-api-object-name
```

#### 5. Business Configuration
```properties
# Business-specific identifiers
property.id=your -property-id
asset.id-your-asset-id
```

#### 6. Scheduling Configuration
```properties
# Scheduler frequency settings
scheduler.single.property.frequency=1
scheduler.d360.sync.frequency=1
```

**Note**: `timeUnit` values (DAYS, MINUTES) must be literal in XML due to schema validation requirements.

#### 7. Content Types
```properties
# Standard content type definitions
content.type.form.urlencoded=application/x-www-form-urlencoded
content.type.json=application/json
```

## Environment-Specific Configurations

### Development Environment (`config-dev.properties`)
- Uses development API endpoints
- More frequent scheduling for testing (every 10 minutes/30 seconds)
- Placeholder values for sensitive data
- Test Salesforce sandbox

### Test Environment (`config-test.properties`)
- Uses test API endpoints
- Moderate scheduling frequency (every 5 minutes/15 seconds)
- Different port configuration (8082)
- Test-specific identifiers

### Production Environment (`config-prod.properties`)
- Uses production API endpoints
- Standard scheduling frequency (daily/minutely)
- Secure property placeholders `${secure::property.name}`
- Production Salesforce org

## Maven Profiles

The project includes Maven profiles for environment management:

### Available Profiles
- `dev` (default) - Development environment
- `test` - Test environment  
- `prod` - Production environment

### Usage Examples
```bash
# Build for development (default)
mvn clean package

# Build for test environment
mvn clean package -Ptest

# Build for production environment
mvn clean package -Pprod

# Deploy to CloudHub with specific environment
mvn clean deploy -DmuleDeploy -Pprod
```

## Global Configuration References

### Updated Connector Configurations
All connector configurations in `global.xml` now reference externalized properties:

```xml
<!-- HTTP Listener Configuration -->
<http:listener-config name="HTTP_Listener_Config">
    <http:listener-connection host="${http.host}" port="${http.port}"/>
</http:listener-config>

<!-- Switchee API Configurations -->
<http:request-config name="Switchee_Auth_API_Config">
    <http:request-connection host="${switchee.auth.api.host}" 
                           port="${switchee.auth.api.port}" 
                           protocol="${switchee.auth.api.protocol}"/>
</http:request-config>

<!-- Salesforce Configuration -->
<salesforce:sfdc-config name="Salesforce_Config">
    <salesforce:basic-connection url="${salesforce.url}" 
                               username="${salesforce.username}" 
                               password="${salesforce.password}"/>
</salesforce:sfdc-config>
```

## Security Considerations

### Production Security
- Use `${secure::property.name}` syntax for sensitive properties in production
- Store actual credentials in secure property files or vault systems
- Never commit sensitive values to version control

### Secure Properties Example
```properties
# Production - use secure property references
salesforce.password=${secure::salesforce.password}
switchee.insights.api.key=${secure::switchee.api.key}
```

## Property Resolution Order

1. System properties (-D command line parameters)
2. Environment variables
3. Property files (based on active Maven profile)
4. Default values in global.xml

## Troubleshooting

### Common Issues

1. **Property not resolved**: Check property name spelling and file inclusion
2. **XML validation errors**: Ensure timeUnit and other enum values are literal, not properties
3. **Profile not active**: Verify Maven profile activation with `mvn help:active-profiles`

### Validation Commands
```bash
# Validate configuration
mvn clean validate

# Test property resolution
mvn help:effective-properties

# Verify active profiles
mvn help:active-profiles
```

## Migration from Hardcoded Values

### What Changed
1. **Removed**: Hardcoded configuration values from flow XML files
2. **Added**: Property placeholders using `${property.name}` syntax
3. **Created**: Environment-specific property files
4. **Updated**: Global connector configurations to use properties
5. **Enhanced**: Maven build process with environment profiles

### Benefits Achieved
- ✅ **Environment Portability**: Single codebase, multiple environments
- ✅ **Security**: Credentials externalized and can be secured
- ✅ **Maintainability**: Configuration changes without code deployment
- ✅ **Compliance**: Follows MuleSoft best practices
- ✅ **DevOps Ready**: Supports CI/CD pipeline configurations

## Best Practices

1. **Naming Convention**: Use dot notation with descriptive prefixes (`switchee.api.host`)
2. **Grouping**: Group related properties together
3. **Documentation**: Comment complex or environment-specific properties
4. **Validation**: Test property resolution in each environment
5. **Security**: Use secure property mechanisms for sensitive data
6. **Version Control**: Never commit sensitive values to repositories

## Support

For questions about configuration management:
1. Refer to MuleSoft documentation: https://docs.mulesoft.com/general/
2. Check Maven profile documentation
3. Validate property resolution using Maven commands
4. Review environment-specific property files for examples