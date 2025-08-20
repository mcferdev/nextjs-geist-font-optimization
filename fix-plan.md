# TypeScript Error Fix Plan

## Issues Identified

### 1. PropertySearchParams Interface Issue
- **File**: `/src/app/my-properties/page.tsx`
- **Error**: `userEmail` does not exist in type `PropertySearchParams`
- **Fix**: Add `userEmail` to `PropertySearchParams` interface

### 2. Prisma Schema Issues
- **File**: `/prisma/schema.prisma`
- **Errors**: 
  - Missing `ListingType` enum
  - Missing fields: `address`, `state`, `zipCode`, `area`, `listingType`
  - Field name mismatches

### 3. PropertyService Type Mismatches
- **File**: `/src/lib/propertyService.ts`
- **Errors**: 
  - Missing `ListingType` import
  - Property field access errors
  - Owner relation issues

## Fix Plan

### Step 1: Update Prisma Schema
- Add missing `ListingType` enum
- Add missing property fields
- Ensure field names match between schema and TypeScript types

### Step 2: Update Type Definitions
- Update `PropertySearchParams` to include `userEmail`
- Ensure consistency between `property.ts` and Prisma schema
- Update `Property` interface to match actual Prisma model

### Step 3: Update PropertyService
- Fix import statements
- Update field mappings to match actual Prisma model
- Fix owner relation handling

### Step 4: Update MyPropertiesPage
- Fix the search parameters to use correct field names
- Ensure proper filtering by user email

### Step 5: Regenerate Prisma Client
- Run `npx prisma generate` to update types
- Run `npx prisma db push` to sync schema changes

## Detailed Changes

### 1. Prisma Schema Updates
```prisma
// Add ListingType enum
enum ListingType {
  SALE
  RENT
}

// Update Property model
model Property {
  id               String      @id @default(cuid())
  title            String
  description      String      @db.Text
  price            Float?
  address          String?     // Add this field
  city             String?     
  neighborhood     String?     
  state            String?     // Add this field
  zipCode          String?     // Add this field (renamed from postcode)
  area             Float?      // Add this field (renamed from totalArea)
  listingType      ListingType // Add this field
  propertyType     PropertyType
  bedrooms         Int?
  bathrooms        Int?
  constructionYear Int?
  floor            Int?
  condominiumFee   Float?
  iptu             Float?
  features         String[]    
  images           Json        @default("[]")
  premiumInfo      String?     @db.Text
  isActive         Boolean     @default(true)
  createdAt        DateTime    @default(now())
  updatedAt        DateTime    @updatedAt
  
  owner   User    @relation(fields: [ownerId], references: [id], onDelete: Cascade)
  ownerId String
  visits  Visit[]
}
```

### 2. Type Definition Updates
- Update `PropertySearchParams` to include `userEmail?: string`
- Update `Property` interface to match Prisma model
- Update field names to match schema

### 3. PropertyService Updates
- Fix import: `import { ListingType } from '@prisma/client'`
- Update field mappings to use correct Prisma field names
- Fix owner relation handling

### 4. MyPropertiesPage Updates
- Update search parameters to filter by owner email
- Ensure proper type usage
