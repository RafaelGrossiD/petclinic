pipeline {
	agent any
	environment {
        DT_TENANT_URL = credentials('DT_TENANT_URL')
        DT_API_TOKEN = credentials('DT_API_TOKEN')
    }
    stages {
        stage('Build on k8 ') {
            steps {           
                        sh 'pwd'
                        sh 'cp -R helm/* .'
		        sh 'ls -ltr'
                        sh 'pwd'
                        sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic  --set image.repository=registry.hub.docker.com/rafaelgd/projectcicd --set image.tag=6'
              		script {	
			def dyna_json = """
			{
			  "title" : "PetClinic",
			  "eventType": "CUSTOM_DEPLOYMENT",
			  "entitySelector": "type(process_group_instance),tag(PetClinic)",
			  "properties": {
			    "Jenkins.JOB_NAME": "${env.JOB_NAME}",
			    "Jenkins.BUILD_NUMBER": "${env.BUILD_NUMBER}",
			    "dt.event.deployment.project": "Project CICD",
			    "dt.event.deployment.remediation_action_link": "https://urlexample",
			    "dt.event.deployment.version": "1.0",
			    "dt.event.deployment.release_stage": "PRD", 
			    "dt.event.deployment.release_product": "Frontend",
			    "approver": "Rafael",
			    "gitcommit": "e5a6baac7eb",
			    "change-request": "CR-42",
			    "dt.event.is_rootcause_relevant": true
			   }
			}
			"""
			
			def dyna_request = httpRequest contentType: 'APPLICATION_JSON', customHeaders: [[maskValue: false, name: 'Authorization', value: "${env.DT_API_TOKEN}"]], httpMode: 'POST', requestBody: dyna_json, responseHandle: 'STRING', url: "${env.DT_TENANT_URL}", validResponseCodes: "100:404"	
			}	
		}           
        }
    }
}
