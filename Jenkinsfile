node {
  komponent = params.komponent

  stage('init') {
    git(credentialsId: 'dat-stash', url: 'ssh://git@stash.devillo.no:7999/dat/test_komponent.git')
  }

  stage('Bygg jUnit') {
    sh(script: 'javac ./test_komponent/javafil')
  }

  stage('Exporter CAR') {
    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'pkg_export', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
      sh(script: """
        ssh srvcis7@e34apvl00342 '/opt/tibco/CIS_7.0/bin/pkg_export.sh -optfile /tdv-u/source/UnitTest_export.opt -user $USERNAME -password $PASSWORD'
        # TODO: Sjekke at dette går bra
      """)
    }
  }

  stage('Kjør JAR+CAR test') {
    sh(script: """
      scp <path-til-jar> srvcis7@e34apvl00342:/home/srvcis7/<navn-på-jar>
      scp srvcis7@e34apvl00342 'java -jar <junit-jar>'
      # TODO: Sjekke at dette går bra
    """)
  }

  stage('Last opp JAR+CAR til Nexus') {
    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'techlab-nexus', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
      sh(script: """
        ssh srvcis7@e34apvl00342 'curl -v --user $USERNAME:$PASSWORD --upload-file <navn-på-car-fil> https://repo.adeo.no/repository/tdv-car/<komponent-navn>/<versjonsnummer>/<navn-på-car>
        ssh srvcis7@e34apvl00342 'curl -v --user $USERNAME:$PASSWORD --upload-file <navn-på-jar-fil> https://repo.adeo.no/repository/tdv-car/<komponent-navn>/<versjonsnummer>/<navn-på-jar>
      """)
    }
  }

  stage('Importer CAR') {
    // TODO: Last ned CAR fra Nexus på riktig maskin
    // Kjør import-script
  }

  stage('Lage changelog') {
    // TODO: Hvordan skal denne lages?
    // Dyttes til Github etterpå
  }
}
