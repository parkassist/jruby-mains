#-*- mode: ruby -*-

id 'org.jruby.mains:jruby-mains'

version '0.5.0'

license :name => 'LGPL3'

developer( :name => 'Christian Meier', :email => 'm.kristian@web.de' )

github = 'jruby/jruby-mains'
scm( :developerConnection => "scm:git:ssh://git@github.com:#{github}.git",
     :connection => "scm:git:https://github.com/#{github}.git",
     :url => "https://github.com/#{github}" )
url 'http://github.com/#{github}'

description 'a set of main method to launch a jruby application from within a jar or war file or start jetty as executable'

scope :provided do
  jar 'org.jruby:jruby:${jruby.version}'
  jar 'org.eclipse.jetty:jetty-server:${jetty.version}'
  jar 'org.eclipse.jetty:jetty-webapp:${jetty.version}'
end

properties( 'jruby.version' => '1.7.23',
            'jetty.version' => '8.1.14.v20131031',
            'project.build.sourceEncoding' => 'utf-8',
            'polyglot.dump.pom' => 'pom.xml' )

plugin :compiler, '2.3.2', :target => '1.7', :source => '1.7'

distribution_management do
  snapshot_repository :id => 'sonatype-nexus-snapshots', :url =>  'https://oss.sonatype.org/content/repositories/snapshots'
  repository :id => 'sonatype-nexus-staging', :url =>  'https://oss.sonatype.org/service/local/staging/deploy/maven2'
end

plugin :deploy, '2.8.2', :skip => '${deploy.skip}'
properties 'deploy.skip' => true

profile :id => 'release' do
  activation do
    property :name => 'performRelease', :value => 'true'
  end

  properties 'deploy.skip' => false

  build do
    default_goal :deploy
  end

  plugin :source, '2.4' do
    execute_goal 'jar-no-fork', :id => 'attach-sources'
  end
  plugin :javadoc, '2.10.1' do
    execute_goal :jar, :id => 'attach-javadocs'
  end
  plugin :gpg, '1.5' do
    execute_goal :sign, :id => 'sign-artifacts', :phase => 'verify'
  end
end

plugin :invoker, '1.8' do
  execute_goals( :install, :run,
                 :id => 'integration-test',
                 :goals => [:verify],
                 :streamLogs => true,
                 :postBuildHookScript =>  'verify.bsh',
                 :cloneProjectsTo => '${project.build.directory}/it',
                 :properties => { 'artifact.version' => '${project.version}',
                   'jruby.version' => '${jruby.version}',
                   'jetty.version' => '${jetty.version}',
                   'jruby.plugins.version' => '1.1.4',
                   'bundler.version' => '1.9.2',
                   # dump pom for the time being - for travis
                   'polyglot.dump.pom' => 'pom.xml'} )
end
