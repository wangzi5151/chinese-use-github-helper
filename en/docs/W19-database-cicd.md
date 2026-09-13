# Database CI/CD Workflow

## Database Migration Management

### Using Prisma

```yaml
# .github/workflows/database.yml
name: Database Migration

on:
  push:
    branches: [main]

jobs:
  migrate:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: test
        ports:
          - 5432:5432
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    
    - run: npm ci
    
    - name: Run migrations
      run: npx prisma migrate deploy
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
    
    - name: Generate Prisma Client
      run: npx prisma generate
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
    
    - name: Run tests
      run: npm test
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
```

### Using Flyway

```yaml
# .github/workflows/flyway.yml
name: Flyway Migration

on:
  push:
    branches: [main]
    paths:
      - 'db/migrations/**'

jobs:
  migrate:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Flyway migrate
      uses: flyway/flyway-actions@v4
      with:
        url: jdbc:postgresql://your-db-host:5432/your-db
        user: ${{ secrets.DB_USER }}
        password: ${{ secrets.DB_PASSWORD }}
        locations: filesystem:db/migrations
```

### Using Liquibase

```yaml
# .github/workflows/liquibase.yml
name: Liquibase Migration

on:
  push:
    branches: [main]

jobs:
  migrate:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Liquibase update
      uses: liquibase/liquibase-github-action@v4
      with:
        command: update
        changelog-file: db/changelog.yaml
        url: jdbc:postgresql://your-db-host:5432/your-db
        username: ${{ secrets.DB_USER }}
        password: ${{ secrets.DB_PASSWORD }}
```

## Database Testing

### Integration Testing

```yaml
# .github/workflows/db-test.yml
name: Database Integration Test

on:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    
    - run: npm ci
    
    - name: Run migrations
      run: npx prisma migrate deploy
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
    
    - name: Seed database
      run: npx prisma db seed
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
    
    - name: Run integration tests
      run: npm run test:integration
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
```

## Database Backup and Recovery

### Automatic Backup

```yaml
# .github/workflows/backup.yml
name: Database Backup

on:
  schedule:
    - cron: '0 2 * * *'  # Every day at 2am

jobs:
  backup:
    runs-on: ubuntu-latest
    
    steps:
    - name: Backup database
      run: |
        pg_dump -h ${{ secrets.DB_HOST }} \
                -U ${{ secrets.DB_USER }} \
                -d ${{ secrets.DB_NAME }} \
                -F c \
                -f backup-$(date +%Y%m%d).dump
    
    - name: Upload backup
      uses: actions/upload-artifact@v4
      with:
        name: backup-${{ github.run_id }}
        path: backup-*.dump
        retention-days: 30
```

### Restore Backup

```bash
# Restore database
pg_restore -h $DB_HOST \
           -U $DB_USER \
           -d $DB_NAME \
           -c backup-20240101.dump
```

## Schema Change Review

### Using Prisma Review

```yaml
# .github/workflows/schema-review.yml
name: Schema Review

on:
  pull_request:
    paths:
      - 'prisma/schema.prisma'

jobs:
  review:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Check schema changes
      run: |
        # Check for breaking changes
        npx prisma format
        npx prisma validate
        
        # Generate migration preview
        npx prisma migrate diff --from-schema-datamodel prisma/schema.prisma --to-schema-datamodel prisma/schema.prisma --exit-code
```

## Best Practices

1. **Use Version Control**: All database changes should be version controlled
2. **Automated Testing**: Run database tests in CI
3. **Backup Strategy**: Regularly backup database
4. **Review Schema Changes**: Review database changes in PRs
5. **Use Migration Tools**: Use professional database migration tools

## Related Resources

- [Prisma Documentation](https://www.prisma.io/docs)
- [Flyway Documentation](https://flywaydb.org/documentation)
- [Liquibase Documentation](https://www.liquibase.org/documentation)