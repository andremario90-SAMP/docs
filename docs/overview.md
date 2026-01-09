# ACN10058 - Overview

## Introduction

ACN10058 is a Visual Basic .NET DLL (Dynamic Link Library) that serves as a **Macro Engine Extension** for the airCalc++ application. This library extends the functionality of the airCalc++ HVAC (Heating, Ventilation, and Air Conditioning) design software by providing custom calculation macros, pricing logic, and component management capabilities.

## Project Information

- **Project Name**: ACN10058
- **Type**: Class Library (DLL)
- **Language**: Visual Basic .NET
- **Framework**: .NET Framework 4.8
- **Platform**: x86
- **Company**: ISC GmbH
- **Product**: airCalc++
- **Version**: 1.0.0.0
- **Copyright**: © 2017

## Purpose

The ACN10058 library provides specialized functionality for:

1. **HVAC Unit Configuration**: Calculating dimensions, positions, and specifications for air handling units (AHU)
2. **Component Pricing**: Computing costs for panels, filters, dampers, coils, and other HVAC components
3. **Weight Calculations**: Determining material weights and quantities for manufacturing
4. **Custom Macros**: Extending airCalc++ with domain-specific business logic
5. **Project Management**: Interfacing with external systems for project tracking and statistics
6. **Material Selection**: Managing material specifications and calculations for different casing types

## Key Features

- **Modular Architecture**: Organized into specialized classes for different calculation domains
- **Database Integration**: Connects to airCalc++ database for material, pricing, and component data
- **Caching System**: Implements caching for materials, weights, prices, and manufacturing times
- **Command Routing**: Dynamic command execution system with prefix-based routing (B_, P_, RFC_, LIB_, SEL_, MSR)
- **External API Integration**: JSON-based communication with external project management systems
- **Macro Wrapper Integration**: Seamless integration with airCalc++ macro system

## Target Application

This DLL is designed to work exclusively with **airCalc++**, a professional HVAC system design and calculation software. It extends the base application with custom calculations specific to ISC GmbH's business requirements.

## Dependencies

The library depends on several external assemblies:

- **AclMkrTools.dll**: airCalc++ macro tools
- **AclMkWrp.dll**: airCalc++ macro wrapper
- **adodb.dll**: ActiveX Data Objects for database operations
- **iscxlsvb6.dll**: Excel integration utilities
- **Newtonsoft.Json.dll**: JSON serialization/deserialization
- **.NET Framework Libraries**: System, System.Data, System.Drawing, System.Windows.Forms, System.Xml

## Output

The compiled DLL is deployed to the `airCalc\ENGINES\` directory where it is loaded by the airCalc++ application at runtime.
