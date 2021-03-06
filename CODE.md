#include <iostream>
#include <string>
#include <dirent.h>

void ProcessDirectory(std::string directory);
void ProcessFile(std::string file);
void ProcessEntity(struct dirent* entity);

std::string path = "/path/to/directory/";

int main()
{
    std::string directory = "theDirectoryYouWant";
    ProcessDirectory(directory);    

    return 0;
}

void ProcessDirectory(std::string directory)
{
    std::string dirToOpen = path + directory;
    auto dir = opendir(dirToOpen.c_str());

    //set the new path for the content of the directory
    path = dirToOpen + "/";

    std::cout << "Process directory: " << dirToOpen.c_str() << std::endl;

    if(NULL == dir)
    {
        std::cout << "could not open directory: " << dirToOpen.c_str() << std::endl;
        return;
    }

    auto entity = readdir(dir);

    while(entity != NULL)
    {
        ProcessEntity(entity);
        entity = readdir(dir);
    }

    //we finished with the directory so remove it from the path
    path.resize(path.length() - 1 - directory.length());
    closedir(dir);
}

void ProcessEntity(struct dirent* entity)
{
    //find entity type
    if(entity->d_type == DT_DIR)
    {//it's an direcotry
        //don't process the  '..' and the '.' directories
        if(entity->d_name[0] == '.')
        {
            return;
        }

        //it's an directory so process it
        ProcessDirectory(std::string(entity->d_name));
        return;
    }

    if(entity->d_type == DT_REG)
    {//regular file
        ProcessFile(std::string(entity->d_name));
        return;
    }

    //there are some other types
    //read here http://linux.die.net/man/3/readdir
    std::cout << "Not a file or directory: " << entity->d_name << std::endl;
}

void ProcessFile(std::string file)
{
    std::cout << "Process file     : " << fileToOpen.c_str() << std::endl;
}
