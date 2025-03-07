# API Integration - Salesforce

public with sharing class ExternalLeadIntegration{
    private static final String API_URL ='https://retoolapi.dev/uxt6NO/ExternalLead';
    
    @future(callout=true)
    public static void fetchLeadData(){
        try{
            Http http = new http();
            HttpRequest request = new HttpRequest();
            request.setEndpoint(API_URL);
            request.setMethod('GET');
            
            HttpResponse response = http.send(request);
            
            System.debug('API Status Code : '+response.getStatusCode());
            System.debug('Response Body : '+response.getBody());
            
            if(response.getStatusCode()==200){
                List<Object> jsonResponse =(List<Object>) JSON.deserializeUntyped(response.getBody());
                List<External_Lead_Data_c__c> leadRecords = new List<External_Lead_Data_c__c>();
                
                for(Object obj:jsonResponse){
                    Map<String,Object> record =(Map<String,Object>) obj; 
                    
                    External_Lead_Data_c__c leadRecord = new External_Lead_Data_c__c();
                    leadRecord.Lead_ID__c =record.containsKey('id') ? Integer.valueOf(record.get('id')) : null;
                    leadRecord.Name__c =record.containsKey('Name') ? String.valueOf(record.get('Name')) : null;
                    leadRecord.Job_Title__c =record.containsKey('Job Title') ? String.valueOf(record.get('Job Title')) : null;
                    leadRecord.Company_Name__c =record.containsKey('Company Name') ? String.valueOf(record.get('Company Name')) : null;
                    leadRecord.Industry__c =record.containsKey('Industry') ? String.valueOf(record.get('Industry')) : null;
                    leadRecord.Email__c =record.containsKey('Email') ? String.valueOf(record.get('Email')) : null;
                    leadRecord.Phone__c =record.containsKey('Phone') ? String.valueOf(record.get('Phone')) : null;
                    
                    leadRecords.add(leadRecord);
                }
                if(!leadRecords.isEmpty()){
                     try {
                        insert leadRecords;
                        System.debug('Inserted ' + leadRecords.size() + ' records successfully.');
                    } catch (DmlException e) {
                        System.debug('DML Exception: ' + e.getMessage());
                    }
                } else {
                    System.debug('No valid data to insert.');
                }
            } else {
                System.debug('HTTP Error: ' + response.getStatus());
            }
        } catch (Exception e) {
            System.debug('Exception Occurred: ' + e.getMessage());
       }
    }
}
